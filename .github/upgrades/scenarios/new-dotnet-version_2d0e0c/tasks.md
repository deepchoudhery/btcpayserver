# BTCPayServer .NET 10.0 Upgrade Tasks

## Overview

This document tracks the execution of the BTCPayServer upgrade from .NET 8.0 to .NET 10.0. All 8 projects will be upgraded simultaneously in a single atomic operation, followed by comprehensive testing and validation.

**Progress**: 0/4 tasks complete (0%) ![0%](https://progress-bar.xyz/0)

---

## Tasks

### [ ] TASK-001: Verify prerequisites
**References**: #plan:Phase-0-Prerequisites

- [ ] (1) Verify .NET 10 SDK is installed per #plan:Phase-0
- [ ] (2) SDK version meets minimum requirements (Verify)
- [ ] (3) Check global.json compatibility with .NET 10 if present
- [ ] (4) Configuration files compatible with target version (Verify)

### [ ] TASK-002: Atomic framework and dependency upgrade
**References**: #plan:Phase-1-Atomic-Upgrade, #plan:Package-Update-Reference, #plan:Breaking-Changes-Catalog

- [ ] (1) Update target framework to net10.0 in all 8 project files per #plan:Step-2
- [ ] (2) All project files updated to net10.0 (Verify)
- [ ] (3) Update package references per #plan:Step-3 (key packages: EF Core 10.0.3, ASP.NET Core 10.0.3, System.IO.Pipelines 10.0.3, System.Text.Json 10.0.3, Newtonsoft.Json 13.0.4)
- [ ] (4) Remove System.Text.RegularExpressions package from BTCPayServer.csproj per #plan:Step-3
- [ ] (5) All package references updated (Verify)
- [ ] (6) Restore all NuGet dependencies per #plan:Step-4
- [ ] (7) All dependencies restored successfully (Verify)
- [ ] (8) Build solution and fix all compilation errors per #plan:Step-5 and #plan:Breaking-Changes-Catalog
- [ ] (9) Solution builds with 0 errors (Verify)

### [ ] TASK-003: Run test suite and validate functionality
**References**: #plan:Phase-2-Test-Validation, #plan:Testing-Strategy

- [ ] (1) Run tests in BTCPayServer.Tests project per #plan:Step-6
- [ ] (2) Fix any test failures (reference #plan:Breaking-Changes-Catalog for URI and HttpContent behavioral changes)
- [ ] (3) Re-run tests after fixes
- [ ] (4) All tests pass with 0 failures (Verify)

### [ ] TASK-004: Final verification and commit
**References**: #plan:Phase-3-Final-Verification, #plan:Source-Control

- [ ] (1) Run full solution build to verify no issues per #plan:Step-7
- [ ] (2) Solution builds with 0 errors and acceptable warnings (Verify)
- [ ] (3) Verify all package dependencies resolved correctly
- [ ] (4) No package dependency conflicts (Verify)
- [ ] (5) Commit all changes with message: "Upgrade solution to .NET 10.0"

---
