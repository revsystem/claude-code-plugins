# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.0.8] - 2026-07-11

### Removed
- **terraform-code-reviewer: dropped aws-documentation-mcp-server**: The agent only needs official-doc lookups and URL citation (`search_documentation` / `read_documentation`), which `aws-knowledge-mcp-server` already covers with better filtering (`topics=reference_documentation`) and broader sources (CDK / constructs.dev). `aws-documentation-mcp-server`'s distinguishing tools (`read_sections`, `recommend`) were unused by this agent, so the dependency was removed from `mcpServers` and the agent prompt now references only `aws-knowledge-mcp-server`. Bumped terraform-code-reviewer to 0.1.3.

## [0.0.7] - 2026-07-07

### Changed
- **terraform-code-reviewer rewrite for Sonnet-class models**: Ported the agent rewrite from claude-code-skills (c3046c2). Pinned `model: sonnet` so reviews run on Sonnet regardless of the orchestrating session's model, translated the description to English, and rewrote the 199-line body down to a scope / check-items / what-not-to-flag / report-format structure. The generic capability enumerations, emoji-based output templates, letter grading, and external-tools section constrained a capable model rather than guiding it.
- **terraform-code-reviewer command/README output examples**: Updated to the new priority-grouped report format (high/medium/low with file:line, code example, reason, and official documentation URL), replacing the emoji summary and letter-grade examples.

## [0.0.6] - 2026-05-25

### Fixed
- **Agent MCP tool grants**: Replaced the invalid single-underscore MCP identifiers (`mcp_<server>`) in agent `tools` fields, which did not match Claude Code's `mcp__<server>` naming and likely blocked MCP access. Agents now use the documented `disallowedTools: Write, Edit` denylist so they reliably inherit their plugin MCP servers (aws-cost-analyst, official-document-checker, terraform-code-reviewer, tech-docs-searcher).
- **aws-cost-analyst MCP reference**: Corrected the agent prompt from the removed `awslabs.cost-explorer-mcp-server` to the configured `awslabs.billing-cost-management-mcp-server`, including its actual tool names (cost-explorer, cost-comparison, cost-anomaly, etc.)

### Changed
- **Read-only reviewers**: Enforced read-only behavior by denying Write/Edit for terraform-code-reviewer, official-document-checker, and tech-docs-searcher, and restricting article-reviewer to `Read, Grep, Glob`. Reviewers now propose changes; the calling session applies them.
- **Agent descriptions**: Rewrote descriptions to trigger-condition style for more reliable automatic delegation.
- **document-reviewer orchestration**: Clarified that the two subagents run in parallel from a single message and do not communicate directly.

## [0.0.5] - 2026-05-14

### Fixed
- **terraform-code-reviewer MCP migration**: Migrated from the yanked `awslabs` Terraform MCP server to the AWS Documentation / Knowledge MCP servers.

## [0.0.4] - 2026-02-11

### Added
- **aws-cost-analyst plugin**: AWS cost analysis, investigation, and cost optimization
  - Cost breakdown and trend analysis for existing AWS resources
  - Anomalous cost investigation and remediation recommendations
  - Cost optimization suggestions (Reserved Instances, Savings Plans, Spot)
  - FinOps best practices with tag-based and regional pricing analysis
- **MCP server integrations for cost analysis**:
  - aws-cost-explorer-mcp-server for cost data retrieval and forecasting
  - aws-pricing-mcp-server for pricing lookups and cost estimation
- **tech-docs-searcher plugin**: Technical documentation search and research
  - AWS Documentation and Knowledge MCP server integration for accurate AWS information
  - Multi-source verification across official docs, GitHub, and technical blogs
  - Support for frameworks, libraries, cloud services (AWS, GCP, Azure), and SaaS products

### Fixed
- **Agent model field**: Changed invalid `model: claude` to `model: inherit` in 5 files
  - agents/article-reviewer.md, agents/official-document-checker.md, agents/terraform-code-reviewer.md
  - commands/document-reviewer.md, commands/terraform-code-reviewer.md
- **Agent frontmatter format**: Fixed aws-cost-analyst.md tools field from YAML list to comma-separated string per official documentation
- **MCP server key consistency**: Aligned JSON server keys with filenames across all MCP configurations
- **Owner email**: Unified owner.email in marketplace.json to `tsuyoshi@rev-system.net`

### Changed
- **MCP server env config**: Use `${AWS_PROFILE:-default}` and `${AWS_REGION:-us-east-1}` environment variable expansion instead of hardcoded placeholders in aws-cost-explorer-mcp-server.json and aws-pricing-mcp-server.json
- Updated README.md with aws-cost-analyst plugin documentation
- Updated marketplace.json to register aws-cost-analyst as independent plugin

## [0.0.3] - 2026-02-07

### Added
- **Terminology standards resource**: Added comprehensive terminology standards file (`agents/resources/terminology-standards.md`)
  - Guidelines for AI/ML, AWS services, programming languages, and tools
  - Rules for proper capitalization, katakana notation, and service name usage
- **Article-reviewer agent enhancement**: Integrated terminology standards into the review process
  - Mandatory reference section for terminology standards
  - Preparation step for loading terminology standards before review
  - Updated terminology unification checklist to reference standards file
- **Enhanced proofreading rules**: Expanded common error corrections and article structure improvement process

### Changed
- **Plugin descriptions**: Updated marketplace.json descriptions to reflect new capabilities
  - document-reviewer: "Review blog articles with terminology standards and AWS documentation verification."
  - metadata: "Provide plugins for reviewing documents and code with terminology standards and official documentation verification."

## [0.0.2] - 2026-01-21

### Added
- **terraform-code-reviewer plugin**: Comprehensive Terraform code analysis and improvement suggestions
  - Security review for resource configurations
  - Best practices validation
  - Performance optimization recommendations
  - Cost optimization analysis

### Changed
- Renamed Terraform reviewer components to 'terraform-code-reviewer' for consistency
- Updated README.md with detailed terraform-code-reviewer documentation

### Fixed
- Package reference in aws-documentation-mcp-server.json (corrected to 'awslabs.aws-documentation-mcp-server')

## [0.0.1] - Initial Release

### Added
- **document-reviewer plugin**: Technical article review functionality
  - Japanese proofreading agent (qiita-article-reviewer-agent)
  - AWS official documentation verification agent (official-document-checker-agent)
  - Integrated review command combining both agents
- **MCP server integrations**:
  - aws-knowledge-mcp-server for AWS service recommendations
  - aws-documentation-mcp-server for official AWS documentation access
- Initial project structure and documentation

[0.0.6]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.5...v0.0.6
[0.0.5]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.4...v0.0.5
[0.0.4]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.3...v0.0.4
[0.0.3]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.2...v0.0.3
[0.0.2]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.1...v0.0.2
[0.0.1]: https://github.com/revsystem/claude-code-plugins/releases/tag/v0.0.1
