# About

## The author

**Wesley Peng** &mdash; software engineer focused on test automation,
LLM agents, and infrastructure-as-code. The Agentic QA Platform is a
personal/professional project that explores how a conversational LLM
agent can replace ticket-driven test environment workflows.

[GitHub: WesleyPeng](https://github.com/WesleyPeng){ .md-button }
[Email: wesley.peng@live.com](mailto:wesley.peng@live.com){ .md-button }

## Why is most of the code private?

The five private repositories
(`agentic-qa-platform`, `agentic-qa-agent`, `qa-dashboard`,
`infra-provisioning`, `jenkins-pipelines`) contain operational details
(vCenter endpoints, IPAM ranges, LLM gateway URLs, and cluster-specific
Sealed Secrets) that aren't safe to publish. Making them public would
either leak those details or require extensive scrubbing on every
commit.

The **architecture, design patterns, and cross-repo coordination
techniques** are all described publicly on this site. The
**framework that exercises the platform**
([agentic-taf](https://github.com/WesleyPeng/agentic-taf)) is fully
open source under LGPL-3.0.

## How to access the private code

If you need read access for collaboration or evaluation:

1. **Email** &mdash; describe the use case to
   [wesley.peng@live.com](mailto:wesley.peng@live.com)
2. **GitHub** &mdash; open an issue on
   [agentic-taf](https://github.com/WesleyPeng/agentic-taf/issues) and
   I'll respond there
3. **Specific code excerpts** &mdash; happy to share specific
   modules or design rationales on request, with sensitive details
   redacted

## License

| Asset | License |
|-------|---------|
| This documentation site (Markdown source + theme config) | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) |
| The `agentic-taf` framework | [LGPL-3.0](https://github.com/WesleyPeng/agentic-taf/blob/main/LICENSE) |
| The five private repos | All-rights-reserved (until publicly released) |
| Architecture diagrams (SVG) | CC BY 4.0 |

## Contributing to this site

The site source lives at
[github.com/WesleyPeng/docs](https://github.com/WesleyPeng/docs). Pull
requests are welcome &mdash; particularly for typos, broken links, or
clarifications. Substantive content changes (new pages, restructured
navigation) are best discussed in an issue first.

## Build details

This site is built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)
and deployed via GitHub Actions on push to `master`. The legacy
[SW QA Automation Recommendations Assessment Report](assessment-report/index.html)
is preserved unchanged at `/docs/assessment-report/`.
