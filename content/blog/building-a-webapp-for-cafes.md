---
title: Building a Web App for Cafes
date: 2023-08-12
excerpt: The inception of cafeist.io dates back to the fourth day of our Phuket sojourn. Today, I am proud to announce that this endeavor has successfully achieved its initial milestone.
keywords:
  - Café discovery app
  - Coffee shop locator
  - Best cafes in Klang Valley
  - Café exploration platform
  - Personalized café lists
---

<Callout emoji="💡">
  This article was enhanced by ChatGPT so pardon the bombastic sentences.
</Callout>

## A Brief Backstory of My Café Adventures

Coffee has always been a source of delight for me. Its aromatic allure and the satisfaction of finding the perfect cup with a delightful taste have always enchanted me.

Earlier this year, my beloved café, which I frequented often, temporarily closed its doors for renovations. This event prompted my partner and me to embark on a café-hunting expedition within our locality.

Our quest began with a Google search: "Best Cafes in Kuala Lumpur." The results unveiled a plethora of articles showcasing the finest cafes in the city for 2023. I even consulted Google Maps to identify the nearest cafes boasting the highest customer reviews. As I documented all these findings in my Notes app, I steadily compiled a list of promising destinations to explore. This ritual continued month after month, gradually wearing me down due to the effort required to curate this growing list.

Jumping forward to just a month ago, my partner and I ventured to Phuket for a combined work and vacation retreat, spanning a week. On the third day of our stay, we decided to wander into the charming Old Town, especially drawn by the renowned night market that graced the town every Sunday. During our journey to the market, I couldn't help but be captivated by the sheer abundance of cafes lining the streets. This experience prompted me to contemplate the implausible task of exploring even a fraction of these enticing cafes if I were to relocate to Phuket.

Following our night market escapade, my mind continued to dwell on the concept of these cafes. The notion of having all these cafes neatly cataloged in one place, with the freedom to pick and choose my destinations for each month, captured my imagination. This marked the birth of [cafeist.io](http://cafeist.io/).

## The Genesis of [cafeist.io](http://cafeist.io/)

The inception of [cafeist.io](http://cafeist.io/) dates back to the fourth day of our Phuket sojourn. Today, I am proud to announce that this endeavor has successfully achieved its initial milestone.

This innovation tackles three fundamental challenges:

- Streamlining the café search process, eliminating unnecessary clutter.
- Crafting personalized lists of preferred cafes.
- Maintaining an organized repository of all the cafes I've explored.

These solutions elegantly address the frustrations I've experienced on a monthly basis.

## Building the Foundation

As I chronicle this journey in my blog, I find it fitting to detail the technological underpinnings of the initial web app build:

On the frontend, I employed:

- [NextJS 13.4](https://nextjs.org/) (utilizing App Router capabilities)
- [TailwindCSS](https://tailwindcss.com/) for crafting stylish designs
- [Shadcn UI](https://ui.shadcn.com/) for assembling user interface components
- [Lucide](https://lucide.dev/) for a captivating array of icons
- [Playwright](http://playwright.dev/) for rigorous end-to-end testing
- [Jest](https://jestjs.io/) for meticulous unit testing

Conversely, the backend is a testament to simplicity:

- [Deno](https://deno.land) for server-side scripting

Indeed, there's no conventional backend in play. I've harnessed the power of [Supabase](https://supabase.com/) to serve as both the backend and database.

Lastly, the culmination of these efforts resides on the [Vercel](https://vercel.com) platform.

The rationale behind my selection of NextJS and TailwindCSS is straightforward: proficiency and mastery. These technologies resonate with me, aligning seamlessly with my skill set. The remaining components on the list might be unfamiliar to me, but I welcome the opportunity to expand my expertise.

## Parting Thoughts

Until now, I've never embraced a side project with such earnest dedication. The inspirations drawn from Product Hunt and Indie Hackers have ignited a newfound passion within me. The examples set by individuals like @levelsio on X (formerly known as Twitter) fuel my conviction that if they can create something significant, so can I. After a three-week hiatus, I am committed to resuming regular postings, as I have several more ideas in the pipeline. These concepts, much like [cafeist.io](http://cafeist.io/), derive from my desire to address everyday challenges.

In conclusion, I invite those interested in real-time updates to follow my microblogging journey on X. Your support means the world as I embark on this exciting endeavor. Let's dive in without hesitation.
