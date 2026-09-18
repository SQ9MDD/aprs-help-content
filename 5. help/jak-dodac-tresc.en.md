---

title: How to add content to APRS.help
description: How to prepare a new article and submit it to the APRS.help repository.
template: doc
tableOfContents: true
---------------------

APRS.help is an open project and anyone can help expand its knowledge base.

The content displayed on the website is stored in the public GitHub repository:

`https://github.com/SQ9MDD/aprs-help-content`

APRS.help synchronizes its content with this repository, so adding or updating an article is done by modifying the appropriate Markdown files and submitting the changes as a **Pull Request**.

## Required language versions

Every new article must be prepared in four language versions:

* Polish
* English
* Spanish
* German

For each article, create four corresponding files:

```text
article-name.pl.md
article-name.en.md
article-name.es.md
article-name.de.md
```

Example:

```text
digipeater.pl.md
digipeater.en.md
digipeater.es.md
digipeater.de.md
```

The file names should be identical and differ only by the language code.

A Pull Request containing a new article should include all four language versions.

If any required language version is missing, the article is considered incomplete and should not be merged into the main repository.

## Document format

Documents must be prepared in **Markdown** (`.md`) format.

Each file should begin with basic metadata describing the document:

```yaml
---
title: Article title
description: Short description of the article.
template: doc
tableOfContents: true
---
```

The `title` and `description` fields should be translated for each language version. The page title is displayed solely from the `title` field, so do not add a second level-one heading (`# Title`) at the start of the article content.

Example of an English version:

```markdown
---
title: APRS Digipeater
description: Basic information about how digipeaters operate in the APRS network.
template: doc
tableOfContents: true
---

A digipeater is a station that retransmits APRS packets received over radio.

## How a digipeater works

Article content...
```

Corresponding files in other languages should preserve the same document structure.

## Where to place the files

All four language versions should be placed in the same directory corresponding to the topic of the article.

Example:

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
```

If you are unsure which section is the best fit, choose the closest topic. The location can be adjusted during Pull Request review.

## How to submit new content

The recommended method is the standard GitHub workflow:

1. Fork the `SQ9MDD/aprs-help-content` repository.
2. Prepare the article in Markdown format.
3. Create the four required language versions: `pl`, `en`, `es` and `de`.
4. Place all files in the appropriate directory.
5. Commit the changes to your repository.
6. Create a **Pull Request** to the main APRS.help repository.
7. Briefly describe what the new or updated content contains.

After review and approval, the Pull Request will be merged into the main repository.

The APRS.help server periodically synchronizes its content with the repository, so accepted changes will subsequently appear on the website.

## Updating existing articles

A Pull Request may also modify existing content, for example by:

* correcting a technical error,
* clarifying a description,
* adding an example,
* fixing a typo,
* updating outdated information,
* expanding an article.

If the change affects the meaning of the content, all language versions of the document should be updated.

## A few rules

When preparing content:

* write clearly and factually,
* divide larger topics using headings,
* use code blocks for APRS frames, configuration examples and commands,
* keep a similar structure across all language versions,
* translate titles, descriptions and headings,
* when describing protocol behavior, provide a source or documentation reference when possible,
* avoid copying large parts of third-party materials,
* for debatable topics, distinguish protocol requirements from best practices and personal experience.

APRS.help is intended to be a shared, practical and technical source of APRS knowledge developed by the amateur radio community.

## Not familiar with GitHub?

That is not a problem.

For simple changes, you can use the GitHub editor directly in your web browser. Installing Git or any additional software is not required.

The most important thing is to prepare useful content and all required language versions. The Pull Request mechanism allows changes to be reviewed together before publication and corrected if necessary.
