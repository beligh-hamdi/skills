# Changelog

## 1.1.0 (2026-07-30)

### Security

* fix critical security vulnerability E005 - remove suspicious download URLs and hardcoded credentials from Maven skill documentation
* fix critical security vulnerability W012 - add comprehensive security warnings for Maven Wrapper runtime binary downloads
* replace all non-official repository URLs with official Apache Maven Central URLs
* add security best practices for repository mirrors, authentication, and proxy configuration
* implement secure credential handling examples using environment variables

### Features

* add Maven unused dependencies analysis skill with comprehensive `dependency:analyze` and `dependency:analyze-only` documentation
* add detailed guidance for dependency cleanup, false positive handling, and CI/CD integration
* enhance Maven skill with security-focused repository configuration examples

### Documentation

* update README with enhanced repository description and skills.sh link (https://www.skills.sh/beligh-hamdi/skills)
* add comprehensive security warnings throughout Maven reference documentation
* improve Maven Wrapper documentation with runtime download risk mitigation strategies
* add enterprise security guidance for Maven repository management and build optimization

## 1.0.0 (2026-06-27)

### Features

* add angular22 skill ([1ce3e3b](https://github.com/beligh-hamdi/skills/commit/1ce3e3bbddb74685eb576e42ea6820e65e998dbd))
* add angular-material-m3 skill ([a499171](https://github.com/beligh-hamdi/skills/commit/a499171))
* add maven skill
