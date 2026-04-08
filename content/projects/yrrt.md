---
title: YNAB Recap Report Tool
date: 2023-06-17
excerpt: A tool to generate recap reports based on YNAB transactions csv.
image: https://res.cloudinary.com/dslokuhdk/image/upload/v1686990661/Screenshot_2023-06-17_at_4.28.11_PM_t0o7dk.png
github: https://github.com/qwerqy/ynab-recap-report-tool
external: https://yrrt.vercel.app/
---

A tool to generate recap reports based on YNAB transactions csv. The following reports will be generated:

    All transactions table
    Total outflow per categories
    Total outflow per flags

This tool will work perfectly when you set a flag for each of your spending transactions. This tool does not process inflow, only outflow (expenses, spending, etc)

Personally I have been using YNAB since 2018. I have always find zero-based budgeting to be awesome and fits my budgeting style. However, I still need a way to

    Plan out how I should spend the next month.
    Revisit my spendings from the past (weeks/months/years)

This project is built with NextJS 13.4 with the App Router. Styling by TailwindCSS.
How to use

    In YNAB, go to All Accounts, select the transactions you want to export.
    Click the title of your budget on the top left of the screen, and click export selected transactions.
    In the dashboard, upload the CSV file and click submit.
    All your data will be shown.
