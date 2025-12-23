# Changelog

All notable changes to this template will be documented in this file.

@see <https://keepachangelog.com/>

## [1.0.2] - 2025-12-23

### Added

- Set filesystem as readonly in the container
- Drop all Linux capabilities
- Handle socket proxy network instead of direct socket access

### Changed

- Use https instead of ssh url to clone repository
- Add "required" column in environment variables listing
- Service specific network commented by default

## [1.0.1] - 2025-12-16

### Fixed

- Timezone sample values corrected

## [1.0.0] - 2025-12-15

### Added

- Docker Compose configuration with Traefik integration
- Environment variables template (`.env.dist`)
- Documentation [README](README.md)
