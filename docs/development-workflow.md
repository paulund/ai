# Development Workflow

## Idea
Description: Initial concept for a project
AFK: No
Skills: `/ai-grill-me`
Context: User's initial idea or concept

## Research
Description: Gather information and resources
AFK: No
Skills:
Context: Outcome of grill me, user-provided resources, and additional research as needed

## Prototyping
Description: Create preliminary version
AFK: No
Skills: `/design-interface`
Context: Research findings and design requirements

## PRD to Issues
Description: Define goals, features, specs, breakdown into vertical slices issues
AFK: No
Skills: `/ai-to-prd`
Context: Outcome of grill me, research and prototyping

## Implementation
Description: Write code and implement features
AFK: Yes
Skills:
    - `/ship`
    - `/review`
    - `/security-review`
Context: Github issue

## QA/Testing
Description: Validate requirements and function
AFK: No
Skills: `/qa` (NOTE: skill does not yet exist on disk — gap to create)
Context: Github PR

## Deployment
Description: Release to users
AFK: Yes
Skills:
    - GitHub Action
    - `/git-github-release`
Context: Merged PR to Main

## Monitoring
Description: Maintain and observe post-launch
AFK: Yes
Skills: `/ops-monitoring`
Context: Log data
