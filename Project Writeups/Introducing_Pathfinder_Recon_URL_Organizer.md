# Introducing Pathfinder: An Open-Source CLI for Organizing Reconnaissance URLs

**Author:** Akshatshirsat
**Published:** August 1, 2026
**Tags:** Bug Bounty, Pentesting, Security, Reconnaissance, OSINT

Every penetration tester and bug bounty hunter eventually runs into the same problem.

After running tools like `gau`, `waybackurls`, `katana`, or other recon pipelines, you're left with tens of thousands—or even millions—of URLs.

<!-- Original article contains an image here. The image asset was not retrievable from the Medium page extraction. -->

Before you can begin testing, you have to answer basic questions:

- Which hosts exist?
- What endpoints are available?
- Which parameters have I discovered?
- How many unique routes are there?
- Where do I even start?

Most of us end up writing ad-hoc scripts, using spreadsheets, or manually filtering the data over and over again.

I built Pathfinder to solve that problem.

## What is Pathfinder?

Pathfinder is an open-source, Linux-first CLI tool that transforms raw reconnaissance URLs into a structured application inventory.

Instead of treating every URL as a separate entry, Pathfinder organizes your reconnaissance data into a clean hierarchy:

<!-- Original article contains an image here. The image asset was not retrievable from the Medium page extraction. -->

The goal is simple: spend less time organizing recon data and more time testing applications.

## Why I Built It

During bug bounty engagements and reconnaissance exercises, I found myself repeating the same workflow:

1. Collect URLs from multiple sources.
2. Remove duplicates.
3. Normalize paths.
4. Identify unique endpoints.
5. Extract parameters.
6. Build an inventory before testing.

There wasn’t a lightweight tool focused solely on creating this inventory. Existing tools either collect URLs or scan them, but the inventory itself often becomes an overlooked step.

Pathfinder fills that gap.

## Current Features

The initial release focuses on building a solid foundation.

It can:

- Import URLs from files or standard input
- Normalize URLs
- Remove duplicate endpoints
- Extract query parameter names
- Build an endpoint tree
- Store everything in SQLite
- Display useful statistics

The project intentionally avoids scanning or sending HTTP requests. Its job is to organize reconnaissance data efficiently.

## Built for Growth

Pathfinder has been designed with a modular architecture from the start.

Future releases will continue building on this foundation with additional capabilities while keeping the core inventory model intact.

## Open Source

Pathfinder is completely open source, and community feedback will shape where it goes next.

If you find bugs, have ideas, or want to contribute, I’d love to hear from you. Feature requests, issue reports, and pull requests are all welcome.

## Try It

**GitHub:** [0xHackshat/Pathfinder](https://github.com/0xHackshat/Pathfinder)

If you find the project useful, consider giving it a ⭐ on GitHub. It helps more people discover the project and motivates future development.

I hope Pathfinder becomes a useful addition to your reconnaissance workflow. I’m looking forward to hearing your feedback and seeing how the community helps shape its future.
