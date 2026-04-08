---
title: Creating Persistent Modals in Next.js with Parallel Routes and Route Interception
date: 2024-11-14
excerpt: In Next.js, creating modals with parallel routes is a well-documented approach, but there's one thing missing from the official docs - how to make the modal persist across page reloads.
keywords:
  - Nextjs modal
  - Nextjs parallel routes
  - Persistent modals in Nextjs
  - Nextjs route interception
  - Nextjs modal with URL support
  - Modals in Nextjs app router
  - Create modals in Nextjs
  - Persistent settings modal Nextjs
  - Nextjs modal navigation
  - Nextjs URL based modal
  - Parallel routing in Nextjs
  - Modal component Nextjs tutorial
  - Advanced modals in Nextjs
  - How to make modal persist in Nextjs
  - Nextjs parallel routes modal setup
  - Nextjs modal open on page refresh
  - Route interception in Nextjs modals
  - Nextjs modal routing example
  - Building modals with App Router Nextjs
  - Modal view with Nextjs parallel routes
---

In Next.js, creating modals with parallel routes is a well-documented approach, but there's one thing missing from the official docs: how to make the modal persist across page reloads. I faced this challenge recently when I was tasked with rebuilding our settings page as a modal, and I wanted to share the approach I took.

## Initial Considerations

When rebuilding the settings as a modal, there were a couple of options:

- **Client-side Modal** - This is straightforward and allows you to manage modals on the client side with state, but it doesn’t update the URL.
- **Parallel Routes** - A more complex approach, but it provides better URL handling, letting users link directly to the modal view.

I chose the second approach to challenge myself and deepen my understanding of the App Router in Next.js. This also means the URL updates when users navigate into the settings modal, which can enhance user experience.

## Folder Structure

Here’s the file structure for implementing this solution (refer to the screenshot for the detailed view):

```scss
app/
├── (authentication)/
├── (dashboard)/
│ ├── @settings/
│ │ ├── (.)settings/
│ │ │ └── profile/
│ │ │ └── page.tsx
│ │ └── settings/
│ │ ├── profile/
│ │ │ └── page.tsx
│ │ └── other-directories...
```

The `@settings` parallel route contains two main directories:

- `(.)settings`: Responsible for rendering the modal view.
- `settings`: Contains core components and functionality, so it can be used across the app without redundancy.

## Steps to Implement Persistent Modals

To get the setup working with a modal that persists through page reloads, here’s what I did:

1. **Move Existing Components**

   Moved everything from `app/settings/` to `app/(dashboard)/@settings/`.

2. **Set Up Layout**

   Removed any existing layout for settings and instead used `app/(dashboard)/@settings/layout.tsx`.

3. **Divide Modal and Core Components**

   The `settings` directory is where all core components live, while `(.)settings` imports those components as needed to render the modal. This keeps our code DRY (Don’t Repeat Yourself).

### Code Examples

`settings/profile/page.tsx`
This file defines the profile page within the core settings structure:

```tsx

export default function ProfilePage() {
  return (
    <div>
      <PageHeader title="Profile" />
      <Profile />
    </div>
  )
}
```

`(.)settings/profile/page.tsx`
This file imports `ProfilePage` to make it accessible within the modal view:

```tsx

export default function Page() {
  return <ProfilePage />
}
```

### Default Page Setup

To ensure that the main page continues to render in the background when the settings modal is open, I added a `default.tsx` in `(dashboard)` and `@settings`.

`(dashboard)/default.tsx`

```tsx

// Ensures the page is rendered in the background when @settings is rendered (e.g., on reload).
export default function Default() {
  return <Page />
}
```

`@settings/default.tsx`

```tsx
export default function Default() {
  return null
}
```

## Why This Works

When a user navigates to the settings modal via a link, `(.)settings` is used, rendering the modal as expected. However, if they reload the page or open the settings in a new tab, `settings` takes over, ensuring that the modal persists across hard reloads.

## Conclusion

Using parallel routes in Next.js for modal creation is powerful and enables better URL handling. With the added structure above, it’s also possible to make the modal persistent, providing a more seamless experience for users even if they refresh the page or open the link in a new tab. This setup not only improves the user experience but also makes it easier to manage the settings modal in a scalable way.
