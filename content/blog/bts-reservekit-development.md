---
title: "ReserveKit Devblog - #1 Building the Foundation"
date: 2025-02-05
excerpt: This post details the initial development of ReserveKit's API and backend, from concept to implementation.
keywords:
  - reservekit
  - api
  - backend
  - development
  - golang
  - react
  - redis
  - rate limiting
  - authentication
---

Hello readers,

I recently launched [ReserveKit](https://reservekit.io), a ready-to-use booking API and backend that integrates seamlessly into any project. While our Product Hunt launch on February 3rd, 2025, didn't generate massive traffic, we did gain some valuable early signups. This post dives into the initial development process, sharing the challenges and solutions encountered while building ReserveKit's foundation.

## The Idea: Solving a Common Problem

The popularity of padel and pickleball has exploded in Malaysia, creating a surge in demand for sports centers. A common thread among these centers is the need for a robust booking system. This need resonated with me, as I'd considered building such a system for a friend's sports center five years prior. Although that project didn't materialize, the idea remained.

The term "booking system" has been on my radar for years. Building one from scratch can be a tedious, time-consuming, and, frankly, unexciting task. In 2023, I even added "SaaS ideas: booking management system" to my notes. The desire to create this tool was always there, and ReserveKit is the result.

## From Zero to API: The First Week

Development began on January 14th, 2025, with the API server, built in Go. Interestingly, this was my first real Go project, and I pushed it straight to production! The API was functional within seven days.

Next came the frontend. I initially opted for [React Router 7](https://reactrouter.com/) for its speed, aiming for client-side rendering. However, I soon realized that all fetch calls were visible in the network tab, which wasn't ideal. I quickly pivoted to server-side rendering (SSR), proxying requests through the backend for better security.

The initial dashboard featured a "Playground" page for testing API endpoints. This page is no longer part of the current dashboard.

![old playground page in dashboard](/static/images/reservekit-playground.png)

## AI Assistance: A Collaborative Effort

AI agents played a significant role in accelerating development. I regularly consulted ChatGPT (versions 4o, o1, and o3), Claude, and Gemini. Over time, I became familiar with the strengths and weaknesses of each, which was an interesting experience in itself.

## Security and Limits: Essential Considerations

My five years of experience with Node.js have given me a deep appreciation for the convenience of npm packages, but also a concern about the potential loss of low-level knowledge. Building a public API server demands a strong focus on security and performance. For ReserveKit, this meant implementing:

- Rate Limiting
- Usage Limits
- Authentication

### Rate Limiting: From Fixed Window to Token Bucket

Initially, I implemented a **Fixed Window algorithm** for rate limiting. While simple to implement, it doesn't handle bursts well. I then transitioned to a **Token Bucket algorithm** with Redis for better burst handling. All algorithms were built from scratch, providing valuable insights into their mechanics. `autocannon` was used for testing.

The initial implementation used fixed capacity and burst limits. Later, I wanted to tie these limits to user subscription packages, which introduced some complexity.

The `IPLimiterMiddleware` initially used fixed limits from the config. However, it needed to **dynamically adjust these limits based on the user's package**. Since this middleware is the first point of contact for incoming requests, it doesn't yet know the user. Performing a database lookup at this stage would add significant **latency**.

```go
func (app *application) IPRateLimiterMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		if app.config.ipLimiter.Enabled {
			// Whitelist checkout endpoint
			if r.URL.Path == "/v1/whitelisted-endpoints" {
				next.ServeHTTP(w, r)
				return
			}

            // Request goes through token bucket algorithm
			if allow, _ := app.ipLimiter.TokenBucket.Allow(r.Context(), r.RemoteAddr); !allow {
				app.rateLimitExceededError(w, r, "rate limit exceeded")
				return
			}
		}

		next.ServeHTTP(w, r)
	})
}
```

In the `Allow` receiver function, the request checks the remote address in the Redis cache via a Lua script. This is ensure this process does not block.

```go
func (s *RateLimitStore) EvaluateIPTokenBucket(ctx context.Context, key string, capacity int64, refillAmount int64, refillPeriod time.Duration) (bool, error) {
	// Lua script for atomic token bucket operations
	script := `
local key = KEYS[1]
local now = tonumber(ARGV[1])
local capacity = tonumber(ARGV[2])
local refillAmount = tonumber(ARGV[3])
local refillPeriod = tonumber(ARGV[4])

-- Get the current bucket state
local bucket = redis.call('HMGET', key, 'tokens', 'lastRefill')
local tokens = tonumber(bucket[1])
local lastRefill = tonumber(bucket[2])

-- Initialize if bucket doesn't exist
if tokens == nil then
	tokens = capacity
	lastRefill = now
end

-- Calculate tokens to add based on time elapsed
local elapsed = now - lastRefill
local periodsElapsed = math.floor(elapsed / refillPeriod)
local tokensToAdd = math.min(capacity - tokens, periodsElapsed * refillAmount)

tokens = tokens + tokensToAdd
lastRefill = lastRefill + (periodsElapsed * refillPeriod)

-- Try to consume a token
if tokens >= 1 then
	tokens = tokens - 1
	redis.call('HMSET', key, 'tokens', tokens, 'lastRefill', lastRefill)
	return 1
end

-- No tokens available
return 0
`

	now := time.Now().UnixNano()
	keys := []string{key}
	args := []interface{}{
		now,
		capacity,
		refillAmount,
		refillPeriod.Nanoseconds(),
	}

	result, err := s.rdb.Eval(ctx, script, keys, args...).Int()
	if err != nil {
		return false, fmt.Errorf("failed to execute rate limit script: %w", err)
	}

	return result == 1, nil
}

```

The solution involved the `APIKeyMiddleware`. This middleware authenticates the API key and, crucially, has access to the user's information, including their package details. It then creates a new Token Bucket instance with the user's specific limits and stores it in Redis. Subsequent requests hit the `IPLimiterMiddleware`, which now finds the user-specific limits in Redis.

```go
func (app *application) APIKeyMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Authenticate API key and other stuff

		// After getting the user and before calling next.ServeHTTP:
		if user != nil {
			// Get rate limit config based on user's package to set dynamic rate limit
			packageTier := config.GetPackageTier(user.Package.Name)

			// Create a token bucket specific to this user
            // This package initializes a new Redis key and value for the user
			userBucket := ratelimiter.NewIPTokenBucket(
				&app.cacheStorage,
				packageTier.Capacity,
				packageTier.Rate,
			)

			// Use the user's ID as the rate limit key
			key := fmt.Sprintf("user_ratelimit:%s", user.ID)

			allowed, err := userBucket.Allow(r.Context(), key)
			if err != nil {
				app.internalServerError(w, r, err)
				return
			}

			if !allowed {
				app.rateLimitExceededError(w, r, "rate limit exceeded")
				return
			}
		}

		ctx = context.WithValue(ctx, userCtx, user)
		ctx = context.WithValue(ctx, apiKeyCtx, apiKey)
		next.ServeHTTP(w, r.WithContext(ctx))
	})
}
```

This approach avoids unnecessary database lookups in the initial middleware and keeps responsibilities clearly defined. This process took about three days of intense work, but the result was worth it.

### Usage Limits: Leaky Buckets and Upside-Down Tokens

I wanted to limit both API usage and booking creation usage. For API usage, I implemented a Leaky Bucket algorithm, storing usage data in Redis by month for easy retrieval on the frontend.

For booking creation usage, I initially used a Token Bucket, but with a twist: the bucket started full, and each booking _removed_ a token. This "upside-down" Token Bucket made displaying usage metrics confusing.

Fortunately, ReserveKit's dependency-injection architecture made it easy to replace the Token Bucket module with a Leaky Bucket module. A valuable lesson in the importance of maintainable code.

> Dependency-injection architecture is also known as Dependency-Inversion Principle (DIP) which is the D (lol) in SOLID principles.
> Read more about it [here](https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design#dependency-inversion-principle)

### Authentication: From JWT to API Keys

Initially, I used JWT tokens for both dashboard and API access. However, I wanted a simpler solution for dashboard authentication that didn't involve token refreshing, so I switched to session cookies.

For API access, JWTs weren't ideal due to their short lifespan. I opted for randomly generated, hashed API keys. The 32-byte keys are hashed before being stored in the database.

The challenge was displaying the key to the user once without compromising security. I considered encrypting the key, but that still meant storing the actual key somewhere. The final solution was to display the plain key once upon creation and store only the hashed version in the database.

## Conclusion

This post covers the initial development of ReserveKit. It was a challenging but rewarding learning experience, and I'm excited to share more updates in future blog posts.
