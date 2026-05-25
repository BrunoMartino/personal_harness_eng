---
name: dependency-guardsman
description: >-
  Enforces npm dependency security scans with Trivy on package-lock.json and
  verifies package licenses against an allow/block list, updating
  open-source-licenses.txt. Use when adding or changing npm dependencies,
  updating package.json, handling package-lock.json, vulnerability scans,
  license compliance, or open-source-licenses.txt.
disable-model-invocation: true
---

# Dependency Guardsman

## When to use

Whenever **adding or changing npm packages** (`package.json` / lockfile flows), invoke this workflow before merging.

## Harness docs first

If the project overrides dependency or security tooling, prefer project docs first (for example harness deployment or operational constraints if present).

## NPM package security scanning

Whenever you add a new npm package to `package.json`, you MUST:

1. Run `npm install --package-lock-only` to update the `package-lock.json` file
2. Run `trivy fs --scanners vuln --severity HIGH,CRITICAL --exit-code 1 package-lock.json` to scan for vulnerabilities
3. If there are HIGH or CRITICAL vulnerabilities found:
   - Search for a newer version of the package that fixes the vulnerability
   - If no fixed version exists, search for and replace with an alternative package that provides similar functionality
   - Re-run the security scan after making changes to ensure vulnerabilities are resolved

This ensures all dependencies are scanned for security vulnerabilities before being added to the codebase.

## NPM package license verification

Whenever you add a new npm package to `package.json`, you MUST verify its license:

1. Check the package's license on npmjs.com or using `npm view <package-name> license`
2. Avoid packages with risky licenses including:
   - GPL (GPL-2.0, GPL-3.0) - Strong copyleft requirements
   - AGPL (AGPL-3.0) - Network copyleft requirements
   - Any "Non-Commercial" licenses
   - Proprietary or unlicensed packages
3. Prefer permissive licenses such as:
   - MIT
   - Apache-2.0
   - BSD (BSD-2-Clause, BSD-3-Clause)
   - ISC
4. After adding a new package, update the `open-source-licenses.txt` file with:
   - Package name
   - Version
   - License type
   - License URL or text location

If a package has a risky license, search for alternative packages with permissive licenses that provide similar functionality.
