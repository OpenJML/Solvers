# Solvers
A collection of SMT solvers used by OpenJML

## Removed: z3-4.5.0.exe, z3-4.6.0.exe (Windows only)

These two Windows builds were removed (2026-08-23). z3-4.5.0/4.6.0 are
still present and tested normally on Solvers-linux and Solvers-macos --
this is a Windows-specific removal only, since jSMTLIB's up-front
solver-availability check treats a missing executable as "skip this
solver here, warn, don't fail" (see LogicTests.solversFromEnv() in the
jSMTLIB repo), so simply not having these two .exe files on Windows is
enough to exclude them there without touching any test configuration.

Reason: on Windows CI, z3-4.5.0.exe and z3-4.6.0.exe produced zero
output for every single test -- no response to even the first command,
and no launch-failure message from jSMTLIB either, consistent with the
process crashing immediately on startup rather than hanging.

Hypothesis (unconfirmed -- no Windows environment was available to
verify directly): these two .exe files need the older Visual C++ 2012
runtime (msvcp110.dll / msvcr110.dll), which isn't bundled here. The
existing Windows z3 builds (4.3.2 through 5.1.0) all share one older
VS2010-era DLL set (msvcp100.dll / msvcr100.dll / vcomp100.dll,
alongside libz3.dll and Microsoft.Z3.dll) already present in
Solvers-windows/, and every other build works fine off that set --
implying z3.exe is normally statically linked and doesn't actually need
those shared DLLs for its own operation. z3-4.5.0/4.6.0 specifically
predate whichever z3 release switched to (or already used) static
linking, so they may be the odd ones out that still need the runtime
DLL dynamically. z3-4.7.1 and later Windows builds do not exhibit this
problem.

If these two are wanted on Windows again, re-adding the matching VS2012
runtime DLLs to Solvers-windows/ alongside the two .exe files (or
switching to a build that doesn't need them) would be the first thing to
try.
