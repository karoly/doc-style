---
title: "Writing Rules for Documents"
type: style-guide
status: living
---

# Writing Rules for Documents

These rules govern every prose document in this repository. The standard behind them: jargon may drift in a year, but clear language drifts in a hundred. Every document is written for a competent reader ten or a hundred years from now, who has no access to today's fashions and no patience for them.

## Rule 1: Complete sentences only

Every prose statement must be a grammatically complete sentence with a subject and a verb.

The most common violation is the announcement fragment: a verbless noun phrase followed by a colon that introduces the real statement. It imitates headline style and it does not survive time or translation.

Bad: "The claim behind it: if a machine turns a specification into code, the specification is the source language."
Good: "The claim behind it is that if a machine turns a specification into code, the specification is the source language."

Bad: "Planned method: mutation testing."
Good: "The planned method is mutation testing."

Bad: "Fallback: build the authoring layer first."
Good: "The fallback is to build the authoring layer first."

A colon is allowed only after a complete clause. "The analogy comes from manufacturing: designing a factory requires brilliant engineers" is correct, because the part before the colon could stand alone as a sentence.

Three terse forms are exempt, because they are structure rather than prose: document metadata headers (the YAML frontmatter block, and any Status, Date, Author, Purpose lines at the top of a file), table cells, and enumerated list items that grammatically continue an introducing sentence. Even in these places, prefer full sentences when space permits.

## Rule 2: Punctuation

Never use an em dash, an en dash, or a double hyphen as punctuation. When the impulse arises, use one of these instead: a colon for a definition or an elaboration; a semicolon between closely related independent clauses; a period and a new sentence; parentheses for an aside; a comma where the pause is light; or rephrase the sentence entirely.

Write ranges with the word "to": "two to six weeks", "90 to 100 %", "steps F1 to F4". Do not write ranges with a dash.

Markdown syntax that happens to contain hyphens (horizontal rules, table separator lines) is machinery, not punctuation, and is exempt.

## Rule 3: Vocabulary

Do not use fashionable shorthand or insider jargon. The test is this: a competent engineer outside our field, or a reader in ten years, must understand the sentence without a glossary.

When a technical term is unavoidable, define it at the first use and then use exactly that term consistently; do not rotate synonyms for variety, because in technical writing a new word implies a new thing.

Proper names of standards, tools, and companies (ReqIF, EARS, Terraform) are allowed and are not jargon; give each a one-clause description at its first mention.

Expand every abbreviation at its first use.

## Rule 4: Claims carry their confidence

Distinguish four kinds of statement and mark each one as what it is: a verified finding (say how it was verified), an inference (say what it was inferred from), a vendor's assertion (name the vendor), or a bet (say which experiment settles it).

Fix numeric decision criteria before the results that they judge exist, so the bar cannot be quietly moved afterward.

## Rule 5: Voice

Prefer the active voice with a named actor. Write "the checking stage rejects the file", not "the file is rejected". A sentence that hides its actor usually hides a design decision too.

## Rule 6: Mechanical checks before committing a document

Run these checks and act on every hit:

1. `grep -n $'\u2014' <file>` (the em dash, U+2014) and `grep -n $'\u2013' <file>` (the en dash, U+2013) must return nothing. Unicode escape codes are used here so that this file contains neither character itself.
2. `grep -nE '^\*?\*?[A-Za-z][^.!?:|]{0,50}:' <file>` is a heuristic that surfaces announcement fragments at the start of prose lines. Review each hit; metadata headers are exempt, prose is not.
3. Read the document aloud. A sentence that cannot be spoken naturally is a sentence to rewrite.

## Rule 7: Documenting an HTTP API

When a document describes an HTTP endpoint, it must be a reference, not a list of paths. A path with a one-line summary is not documentation; a caller who reads it still cannot make a request or handle a failure. Every endpoint, or a tight group of endpoints that share one payload shape, carries five things:

1. **A heading with the method and path**, for example `POST /v1/chat/completions`. Path parameters appear in the path as `:name`.
2. **One sentence** stating what it does and who may call it (the required key, token, or role).
3. **Request.** The content type, the path and query parameters (each with its type, whether it is required, and its meaning), and the request body shown as a small annotated JSON example or a field table (name, type, required, meaning). Omit the body line for endpoints that take none.
4. **Response.** The success status code and the response body shown as an annotated JSON example or field table, plus any notable response headers.
5. **Errors.** A table of the status codes this endpoint can return, each row giving the error code or string the body carries, what causes it, and how the caller resolves it. "How the caller resolves it" is not optional: an error the reader cannot act on is a defect in the documentation.

Two conventions keep this from bloating:

- **Document a resource once.** When many endpoints operate on the same resource (a model, an alias, a user), show the resource JSON in full once, then describe each operation as its delta (what it reads from the body, what it returns) rather than repeating the whole shape.
- **Share the common errors.** Errors that apply to a whole surface (authentication, budget, rate limiting) are documented once in a surface-level error table; per-endpoint error tables then list only the errors specific to that endpoint.

This structure is exempt from Rule 1 in its field labels only ("Request.", "Response.", "Errors." and the column headers are structural, like table cells); the meanings and resolutions written inside them are prose and follow every other rule.
