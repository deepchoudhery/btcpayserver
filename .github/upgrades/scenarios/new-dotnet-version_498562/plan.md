# .NET 10 Upgrade Plan for BTCPayServer

**Generated:** 2026-02-17  
**Target Framework:** .NET 10.0 (Long Term Support)  
**Current State:** All projects on .NET 8.0 (except BTCPayServer.Client on netstandard2.1)  
**Total Projects:** 8  
**Total Packages to Update:** 9

---

## Executive Summary

This plan outlines the upgrade of the BTCPayServer solution from .NET 8.0 to .NET 10.0. The upgrade is rated as **Low Complexity** due to:
- All projects are already SDK-style (no conversion needed)
- Minimal API breaking changes (39 behavioral changes, no binary breaks)
- Small percentage of code requiring modification (0.0% of 170K LOC)
- All packages have compatible versions or upgrade paths
- No security vulnerabilities to address during upgrade

The upgrade will be executed using an **All-At-Once** strategy, updating all projects simultaneously due to the small number of projects (8), simple dependency structure, and low risk profile.

---

## Upgrade Strategy: All-At-Once

### Rationale for All-At-Once Approach

**Selected Strategy:** All-At-Once  
**Confidence:** High

**Reasons:**
1. **Small Solution Size:** Only 8 projects make coordination manageable
2. **Low Complexity:** All projects rated as "Low" difficulty
3. **Clear Dependencies:** Linear dependency chain with no circular dependencies
4. **Minimal Risk:** 
   - No binary breaking changes
   - Only 39 behavioral API changes (low impact)
   - 82.4% of packages already compatible
5. **Faster Timeline:** Single comprehensive upgrade and test cycle
6. **SDK-Style Projects:** All projects already modernized to SDK-style

**Execution Approach:**
- Update all project target frameworks in dependency order
- Update all package versions across all projects
- Build solution and address any compilation issues
- Run comprehensive test suite
- Address behavioral changes through testing

---

## Dependency Analysis

### Project Dependency Graph

Based on topological analysis, the dependency order is:

**Phase 1: Leaf Projects (No Dependencies)**
1. BTCPayServer.Client (netstandard2.1)

**Phase 2: Foundation Libraries**
2. BTCPayServer.Abstractions (depends on: BTCPayServer.Client)
3. BTCPayServer.Common (depends on: none)
4. BTCPayServer.Rating (depends on: BTCPayServer.Client)

**Phase 3: Data Layer**
5. BTCPayServer.Data (depends on: BTCPayServer.Abstractions, BTCPayServer.Client)

**Phase 4: Main Application**
6. BTCPayServer (depends on: all above except BTCPayServer.Common, BTCPayServer.Rating)

**Phase 5: Tooling and Tests**
7. BTCPayServer.PluginPacker (depends on: BTCPayServer)
8. BTCPayServer.Tests (depends on: BTCPayServer)

### Dependency Insights

- **BTCPayServer.Client** is a foundational library used by most projects
  - Currently targets netstandard2.1 (cross-platform library)
  - **Decision:** Keep netstandard2.1 for maximum compatibility, or upgrade to net10.0
  - **Recommendation:** Evaluate if cross-platform compatibility is still required
  
- **BTCPayServer.Common** has no project dependencies
  - Can be upgraded independently
  - Low risk

- **BTCPayServer** is the main application depending on most libraries
  - Requires all dependencies to be upgraded first conceptually
  - With all-at-once strategy, all projects upgraded together

---

## Package Update Strategy

### Packages Requiring Updates

| Package | Current | Target | Projects Affected | Priority |
|---------|---------|--------|-------------------|----------|
| Microsoft.AspNetCore.Identity.EntityFrameworkCore | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| Microsoft.AspNetCore.Mvc.NewtonsoftJson | 8.0.11 | 10.0.3 | BTCPayServer | High |
| Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation | 8.0.11 | 10.0.3 | BTCPayServer, BTCPayServer.Tests | High |
| Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson | 8.0.11 | 10.0.3 | BTCPayServer | High |
| Microsoft.EntityFrameworkCore | 8.0.11 | 10.0.3 | BTCPayServer.Abstractions | High |
| Microsoft.EntityFrameworkCore.Design | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| System.IO.Pipelines | 8.0.0 | 10.0.3 | BTCPayServer | Medium |
| System.Text.Json | 8.0.5 | 10.0.3 | BTCPayServer.Rating | Medium |
| Newtonsoft.Json | 13.0.3 | 13.0.4 | BTCPayServer.Client, BTCPayServer, BTCPayServer.Rating | Low |

### Package Update Approach

All packages will be updated as part of the upgrade:
- **Microsoft.AspNetCore.*** packages:** Essential for ASP.NET Core 10 compatibility
- **Microsoft.EntityFrameworkCore.*** packages:** Must align with framework version
- **System.*** packages:** Framework-aligned versions for optimal performance
- **Newtonsoft.Json:** Minor version bump for bug fixes

**No packages will be deferred** - all updates are included in this upgrade.

### Packages to Remove

| Package | Reason | Project |
|---------|--------|---------|
| System.Text.RegularExpressions | Functionality included in framework | BTCPayServer |

This package is redundant in .NET 10 and should be removed from BTCPayServer.csproj.

---

## Project-by-Project Upgrade Plans

### 1. BTCPayServer.Client

**Current Target:** netstandard2.1  
**Proposed Target:** **Decision Required** - Keep netstandard2.1 OR upgrade to net10.0

**Type:** ClassLibrary  
**SDK Style:** Yes  
**Dependencies:** None (leaf project)  
**LOC:** ~7,500  
**Risk:** Low

**Changes Required:**
1. **If keeping netstandard2.1:**
   - Update Newtonsoft.Json: 13.0.3 → 13.0.4
   - No framework change needed
   - Verify compatibility with .NET 10 projects

2. **If upgrading to net10.0:**
   - Update TargetFramework: netstandard2.1 → net10.0
   - Update Newtonsoft.Json: 13.0.3 → 13.0.4
   - Review cross-platform requirements

**API Impact:**
- 39 behavioral changes in System.Uri and HttpContent
- Review usage of these APIs in client library
- Update documentation if behavior changes affect consumers

**Testing:**
- Unit tests should pass
- Verify client library works with .NET 10 consumers

### 2. BTCPayServer.Common

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** ClassLibrary  
**SDK Style:** Yes  
**Dependencies:** None  
**LOC:** ~1,000  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. No package updates required

**API Impact:** None identified

**Testing:**
- Build verification
- Dependent projects build correctly

### 3. BTCPayServer.Abstractions

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** ClassLibrary  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer.Client  
**LOC:** ~2,500  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. Update Microsoft.EntityFrameworkCore: 8.0.11 → 10.0.3

**API Impact:** None identified

**Testing:**
- Build verification
- EF Core functionality intact
- Dependent projects build correctly

### 4. BTCPayServer.Rating

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** ClassLibrary  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer.Client  
**LOC:** ~3,000  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. Update System.Text.Json: 8.0.5 → 10.0.3
3. Update Newtonsoft.Json: 13.0.3 → 13.0.4

**API Impact:** None identified

**Testing:**
- Rating calculations accuracy
- JSON serialization/deserialization works correctly
- Exchange rate fetching functionality

### 5. BTCPayServer.Data

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** ClassLibrary  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer.Abstractions, BTCPayServer.Client  
**LOC:** ~8,000  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. Update Microsoft.AspNetCore.Identity.EntityFrameworkCore: 8.0.11 → 10.0.3
3. Update Microsoft.EntityFrameworkCore.Design: 8.0.11 → 10.0.3

**API Impact:** None identified

**Entity Framework Considerations:**
- Review existing migrations for compatibility
- May need to regenerate migrations if EF Core 10 changes migration format
- Test database connectivity and CRUD operations

**Testing:**
- All database operations
- Identity functionality
- Migration scripts (if regenerated)

### 6. BTCPayServer (Main Application)

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** AspNetCore  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer.Abstractions, BTCPayServer.Client, BTCPayServer.Data, BTCPayServer.Rating  
**LOC:** ~145,000  
**Risk:** Medium (due to size, but low technical risk)

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. Update Microsoft.AspNetCore.Mvc.NewtonsoftJson: 8.0.11 → 10.0.3
3. Update Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation: 8.0.11 → 10.0.3
4. Update Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson: 8.0.11 → 10.0.3
5. Update System.IO.Pipelines: 8.0.0 → 10.0.3
6. Update Newtonsoft.Json: 13.0.3 → 13.0.4
7. **Remove** System.Text.RegularExpressions (included in framework)

**API Impact:** None identified in compilation

**ASP.NET Core Considerations:**
- Review middleware pipeline for any deprecated patterns
- Check authentication/authorization configuration
- Verify SignalR functionality with updated packages
- Test Razor runtime compilation

**Testing:**
- Full functional testing of main application
- All HTTP endpoints
- SignalR real-time functionality
- Payment processing workflows
- Plugin loading and execution
- Admin panel functionality

### 7. BTCPayServer.PluginPacker

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** DotNetCoreApp (Tool)  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer  
**LOC:** ~500  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. No package updates required

**API Impact:** None identified

**Testing:**
- Plugin packing functionality
- Generated plugin packages are valid

### 8. BTCPayServer.Tests

**Current Target:** net8.0  
**Proposed Target:** net10.0

**Type:** AspNetCore (Test Project)  
**SDK Style:** Yes  
**Dependencies:** BTCPayServer  
**LOC:** ~2,500  
**Risk:** Low

**Changes Required:**
1. Update TargetFramework: net8.0 → net10.0
2. Update Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation: 8.0.11 → 10.0.3

**API Impact:** None identified

**Testing:**
- All unit tests pass
- Integration tests pass
- Playwright tests pass

---

## Breaking Changes Analysis

### .NET 8 to .NET 10 Known Breaking Changes

Based on Microsoft documentation and assessment results:

**1. System.Uri Behavioral Changes (32 occurrences)**
- **Impact:** Low - Behavioral changes in URI parsing and formatting
- **Location:** Primarily in BTCPayServer.Client
- **Mitigation:** Review URI handling code, add runtime tests
- **Action Required:** Test API clients and URL generation

**2. System.Net.Http.HttpContent Behavioral Changes (7 occurrences)**
- **Impact:** Low - Changes in HTTP content handling
- **Location:** API client code
- **Mitigation:** Verify HTTP request/response handling
- **Action Required:** Test API communication

**3. ASP.NET Core Changes**
- **Impact:** Low - Framework updates are typically backward compatible
- **Mitigation:** Test middleware pipeline and routing
- **Action Required:** Full integration testing

**4. Entity Framework Core 10**
- **Impact:** Low - Minor improvements, no major breaks expected
- **Mitigation:** Test database operations
- **Action Required:** Verify migrations and queries

### Breaking Changes by Category

| Category | Count | Impact | Mitigation |
|----------|-------|--------|------------|
| Behavioral Changes | 39 | Low | Runtime testing |
| Binary Breaking | 0 | None | N/A |
| Source Breaking | 0 | None | N/A |
| Configuration | TBD | Low | Review startup code |

---

## Testing Strategy

### Multi-Level Testing Approach

#### Level 1: Per-Project Compilation
After updating each project's framework and packages:
- [ ] Project builds without errors
- [ ] Project builds without warnings
- [ ] No package restore issues
- [ ] No dependency conflicts

#### Level 2: Solution Build
After all projects updated:
- [ ] Full solution builds successfully
- [ ] No inter-project dependency issues
- [ ] All projects target correct framework versions

#### Level 3: Unit Testing
- [ ] Run full test suite from BTCPayServer.Tests
- [ ] All existing tests pass
- [ ] No test framework compatibility issues

#### Level 4: Integration Testing
- [ ] Playwright end-to-end tests pass
- [ ] API client tests pass
- [ ] Database migrations work
- [ ] Authentication/authorization works

#### Level 5: Functional Validation
- [ ] Application starts successfully
- [ ] Payment workflows function correctly
- [ ] Plugin system works
- [ ] Admin panel accessible and functional
- [ ] SignalR real-time updates work
- [ ] External integrations (Lightning, Bitcoin) work

#### Level 6: Behavioral Change Verification

**System.Uri Changes:**
- [ ] Test URL parsing in payment processors
- [ ] Verify API endpoint URL generation
- [ ] Check redirect URLs in authentication

**HttpContent Changes:**
- [ ] Test API client requests/responses
- [ ] Verify webhook payloads
- [ ] Check external API integrations

### Test Execution Plan

1. **Build Verification** (~5 minutes)
   - Clean build of entire solution
   - Verify no compilation errors or warnings

2. **Unit Tests** (~10 minutes)
   - Run BTCPayServer.Tests project
   - Verify all tests pass

3. **Integration Tests** (~30 minutes)
   - Run Playwright tests
   - Test database operations
   - Verify plugin loading

4. **Manual Functional Testing** (~60 minutes)
   - Start application locally
   - Test core payment flows
   - Verify admin functionality
   - Test external integrations

### Success Criteria

The upgrade is successful when:
- [x] All 8 projects target .NET 10.0 (or netstandard2.1 for Client if kept)
- [x] All 9 package updates applied
- [x] Solution builds with zero errors
- [x] Solution builds with zero warnings
- [x] All unit tests pass (100%)
- [x] All integration tests pass (100%)
- [x] Application starts and runs correctly
- [x] No functional regressions detected
- [x] No performance degradation

---

## Risk Assessment and Mitigation

### Project Risk Analysis

| Project | Risk Level | Risk Factors | Mitigation |
|---------|-----------|--------------|------------|
| BTCPayServer.Client | Medium | netstandard2.1 decision, 39 API changes | Thorough testing of API clients |
| BTCPayServer.Common | Low | No dependencies, no packages | Basic build verification |
| BTCPayServer.Abstractions | Low | Single EF Core package | Test EF functionality |
| BTCPayServer.Rating | Low | Two package updates | Test rating calculations |
| BTCPayServer.Data | Low | EF Core Identity packages | Test auth and migrations |
| BTCPayServer | Medium | Large codebase, 6 packages | Comprehensive testing |
| BTCPayServer.PluginPacker | Low | Tool project, no packages | Test plugin packing |
| BTCPayServer.Tests | Low | Test project | Ensure tests still run |

### Risk Categories

**Technical Risks:**
1. **API Behavioral Changes** (Medium)
   - 39 API changes in System.Uri and HttpContent
   - **Mitigation:** Comprehensive testing of affected code paths
   - **Rollback:** Keep .NET 8 branch available

2. **Package Compatibility** (Low)
   - All packages have .NET 10 compatible versions
   - **Mitigation:** Verify package functionality during testing
   - **Rollback:** Documented package version downgrades

3. **Entity Framework Changes** (Low)
   - EF Core 10 may have subtle changes
   - **Mitigation:** Test all database operations
   - **Rollback:** Database migrations can be reverted

**Operational Risks:**
1. **Breaking Production** (Low)
   - Changes are in upgrade branch
   - **Mitigation:** Thorough testing before merge
   - **Rollback:** Keep main branch on .NET 8

2. **Test Failures** (Medium)
   - Tests may need updates for behavioral changes
   - **Mitigation:** Update tests as needed
   - **Rollback:** Document test changes

### Rollback Plan

If critical issues are discovered:

1. **Before Merge:**
   - Stay on upgrade branch
   - Fix issues or abandon upgrade
   - Main branch remains on .NET 8

2. **After Merge:**
   - Revert merge commit
   - Return to .NET 8 version
   - Investigate issues offline

3. **In Production:**
   - Redeploy previous .NET 8 version
   - Database migrations may need manual rollback
   - Document issues for retry

---

## Upgrade Execution Sequence

### Phase 1: Preparation
1. Ensure working tree is clean (no uncommitted changes)
2. Verify currently on upgrade branch
3. Back up current state (git tag)

### Phase 2: Framework Updates (All Projects)
Execute in dependency order, but all at once:

1. **BTCPayServer.Client**
   - Decision: Keep netstandard2.1 OR upgrade to net10.0
   - Update packages: Newtonsoft.Json

2. **BTCPayServer.Common**
   - Update TargetFramework: net10.0

3. **BTCPayServer.Abstractions**
   - Update TargetFramework: net10.0
   - Update packages: Microsoft.EntityFrameworkCore

4. **BTCPayServer.Rating**
   - Update TargetFramework: net10.0
   - Update packages: System.Text.Json, Newtonsoft.Json

5. **BTCPayServer.Data**
   - Update TargetFramework: net10.0
   - Update packages: Microsoft.AspNetCore.Identity.EntityFrameworkCore, Microsoft.EntityFrameworkCore.Design

6. **BTCPayServer**
   - Update TargetFramework: net10.0
   - Update packages: 6 packages
   - Remove package: System.Text.RegularExpressions

7. **BTCPayServer.PluginPacker**
   - Update TargetFramework: net10.0

8. **BTCPayServer.Tests**
   - Update TargetFramework: net10.0
   - Update packages: Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation

### Phase 3: Build Verification
1. Clean solution
2. Restore NuGet packages
3. Build entire solution
4. Fix any compilation errors
5. Address any warnings

### Phase 4: Testing
1. Run unit tests (BTCPayServer.Tests)
2. Run integration tests
3. Run Playwright tests
4. Manual functional testing
5. Behavioral change verification

### Phase 5: Documentation
1. Update README if needed
2. Update build documentation
3. Document any breaking changes found
4. Update deployment guides

### Phase 6: Finalization
1. Commit all changes
2. Update PR description
3. Request code review
4. Address feedback
5. Merge to main branch

---

## Timeline and Effort Estimation

### Estimated Timeline

| Phase | Duration | Description |
|-------|----------|-------------|
| Preparation | 15 min | Verify environment, create backup |
| Framework Updates | 30 min | Update all 8 project files |
| Package Updates | 30 min | Update 9 packages, remove 1 |
| Build & Fix | 30 min | Build solution, fix compilation issues |
| Unit Testing | 30 min | Run and fix unit tests |
| Integration Testing | 60 min | Run and fix integration tests |
| Functional Testing | 90 min | Manual testing of application |
| Documentation | 30 min | Update relevant docs |
| **Total** | **5 hours** | Estimated total effort |

### Assumptions
- No major unexpected breaking changes
- Test infrastructure works with .NET 10
- All package updates are straightforward
- No blocking issues discovered

### Buffer Time
- Add 2-3 hours buffer for unexpected issues
- **Realistic Total:** 7-8 hours

---

## Decision Points

### Critical Decisions Required

**1. BTCPayServer.Client Target Framework**

**Options:**
- **Option A:** Keep netstandard2.1 (current)
  - **Pros:** Maximum compatibility, can be used by .NET Framework consumers
  - **Cons:** Limited to netstandard2.1 APIs
  
- **Option B:** Upgrade to net10.0
  - **Pros:** Access to latest .NET APIs, better performance
  - **Cons:** Requires .NET 10+ consumers, breaking change for external users

**Recommendation:** Keep netstandard2.1 unless there's a specific need for .NET 10 features. This is a public client library that may have external consumers.

**Decision Required Before Execution:** Yes

---

## Success Metrics

### Quantitative Metrics
- [ ] 8/8 projects successfully upgraded to .NET 10 (or Client kept on netstandard2.1)
- [ ] 9/9 packages updated to .NET 10 compatible versions
- [ ] 1/1 redundant package removed
- [ ] 0 build errors
- [ ] 0 build warnings
- [ ] 100% unit tests passing
- [ ] 100% integration tests passing
- [ ] 0 functional regressions

### Qualitative Metrics
- [ ] Application performance is equal to or better than .NET 8
- [ ] No degradation in user experience
- [ ] Code quality maintained or improved
- [ ] Documentation is up to date

---

## Appendix

### Useful References
- [.NET 10 Release Notes](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-10)
- [ASP.NET Core 10 Migration Guide](https://learn.microsoft.com/aspnet/core/migration/90-to-100)
- [Entity Framework Core 10 What's New](https://learn.microsoft.com/ef/core/what-is-new/ef-core-10.0/whatsnew)
- [.NET 10 Breaking Changes](https://learn.microsoft.com/dotnet/core/compatibility/10.0)

### Contact and Escalation
- For blocking issues, consult the BTCPayServer development team
- For .NET-specific questions, refer to Microsoft documentation
- For package-specific issues, check package repository issue trackers

---

**Plan Status:** Ready for Execution  
**Next Step:** Begin Execution Phase with task generation and implementation
