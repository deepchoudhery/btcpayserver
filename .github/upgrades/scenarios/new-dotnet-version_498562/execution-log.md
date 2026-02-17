
## [2026-02-17 07:10] TASK-002: Atomic framework and package upgrade

Status: In Progress

**Files Modified**:
- Build/Common.csproj: TargetFramework net8.0 → net10.0
- BTCPayServer.Client/BTCPayServer.Client.csproj: Newtonsoft.Json 13.0.3 → 13.0.4
- BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj: Microsoft.EntityFrameworkCore 8.0.11 → 10.0.3
- BTCPayServer.Data/BTCPayServer.Data.csproj: Microsoft.EntityFrameworkCore.Design 8.0.11 → 10.0.3, Microsoft.AspNetCore.Identity.EntityFrameworkCore 8.0.11 → 10.0.3
- BTCPayServer.Rating/BTCPayServer.Rating.csproj: Newtonsoft.Json 13.0.3 → 13.0.4, System.Text.Json 8.0.5 → 10.0.3
- BTCPayServer/BTCPayServer.csproj: Updated 4 Microsoft.AspNetCore packages to 10.0.3, System.IO.Pipelines 8.0.0 → 10.0.3, Newtonsoft.Json 13.0.3 → 13.0.4, removed System.Text.RegularExpressions
- BTCPayServer.Tests/BTCPayServer.Tests.csproj: Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation 8.0.11 → 10.0.3

Partial - committing now before continuing

