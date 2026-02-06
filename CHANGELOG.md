# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

[0.0.3]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.2...v0.0.3
[0.0.2]: https://github.com/revsystem/claude-code-plugins/compare/v0.0.1...v0.0.2
[0.0.1]: https://github.com/revsystem/claude-code-plugins/releases/tag/v0.0.1
