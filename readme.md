# Java Library Remediation Proof of Concept

This is a simple Maven-based Java project demonstrating library remediation with specific versions and custom repositories.

## Prerequisites

- Java 21
- Maven

## Project Details

- **Group ID**: `com.edw`
- **Artifact ID**: `java-lib-remediation-poc`
- **Version**: `1.0-SNAPSHOT`

## Build and Package

To build the project and generate the executable JAR file, run the following command from the root directory:

```bash
mvn clean package -s settings.xml
```

The `-s settings.xml` flag ensures that the build uses the custom repository configurations defined in the project's settings file.

## Running the Application

After a successful build, you can run the application using:

```bash
java -cp target/java-lib-remediation-poc-1.0-SNAPSHOT.jar com.edw.Main
```

Expected Output:
```text
Hello World!
```
## Scan Methodology

### Trivy
```
$ trivy -v
Version: dev
Vulnerability DB:
  Version: 2
  UpdatedAt: 2026-09-15 07:08:16.463168542 +0000 UTC
  NextUpdate: 2026-09-16 07:08:16.463168061 +0000 UTC
  DownloadedAt: 2026-09-15 08:04:48.355086221 +0000 UTC

$ trivy fs .
.....
pom.xml (pom)

Total: 6 (UNKNOWN: 0, LOW: 1, MEDIUM: 2, HIGH: 3, CRITICAL: 0)

┌──────────────────────────────────────┬────────────────┬──────────┬────────┬─────────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│               Library                │ Vulnerability  │ Severity │ Status │    Installed Version    │ Fixed Version │                            Title                             │
├──────────────────────────────────────┼────────────────┼──────────┼────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ com.fasterxml.woodstox:woodstox-core │ CVE-2022-40152 │ MEDIUM   │ fixed  │ 6.0.3.rhlw-00001        │ 6.4.0, 5.4.0  │ woodstox-core: woodstox to serialise XML data was vulnerable │
│                                      │                │          │        │                         │               │ to Denial of Service...                                      │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2022-40152                   │
├──────────────────────────────────────┼────────────────┤          │        ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ com.jayway.jsonpath:json-path        │ CVE-2023-51074 │          │        │ 2.8.0.rhlw-00001        │ 2.9.0         │ json-path: stack-based buffer overflow in Criteria.parse     │
│                                      │                │          │        │                         │               │ method                                                       │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2023-51074                   │
├──────────────────────────────────────┼────────────────┼──────────┤        ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ org.json:json                        │ CVE-2022-45688 │ HIGH     │        │ 20220320.0.0.rhlw-00003 │ 20230227      │ json stack overflow vulnerability                            │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2022-45688                   │
│                                      ├────────────────┤          │        │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                                      │ CVE-2023-5072  │          │        │                         │ 20231013      │ JSON-java: parser confusion leads to OOM                     │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2023-5072                    │
├──────────────────────────────────────┼────────────────┤          │        ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ org.springframework:spring-core      │ CVE-2025-41249 │          │        │ 5.3.18.rhlw-00003       │ 6.2.11        │ org.springframework/spring-core: Spring Framework Annotation │
│                                      │                │          │        │                         │               │ Detection Vulnerability                                      │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2025-41249                   │
│                                      ├────────────────┼──────────┤        │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                                      │ CVE-2026-41848 │ LOW      │        │                         │ 7.0.8, 6.2.19 │ spring-framework: Spring Framework: Regular Expression       │
│                                      │                │          │        │                         │               │ Denial of Service in AntPathMatcher                          │
│                                      │                │          │        │                         │               │ https://avd.aquasec.com/nvd/cve-2026-41848                   │
└──────────────────────────────────────┴────────────────┴──────────┴────────┴─────────────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘

```

### Semgrep
```
$ semgrep --version
1.176.1

$ semgrep ci --supply-chain
....
Finalizing scan

┌──────────────────────────────────┐
│ 1 Reachable Supply Chain Finding │
└──────────────────────────────────┘

    maven_dep_tree.txt
   ❯❯❱ org.json:json - CVE-2023-5072
          Severity: HIGH
          Affected versions of org.json:json are vulnerable to a denial of service (DoS) attack.

           ▶▶┆ Fixed for org.json:json at version: 20231013
            9┆ +- org.json:json:jar:20220320.0.0.rhlw-00003:compile


┌──────────────────────────────────────┐
│ 2 Undetermined Supply Chain Findings │
└──────────────────────────────────────┘

    maven_dep_tree.txt
    ❯❱ com.fasterxml.woodstox:woodstox-core - CVE-2022-40152
          Severity: MODERATE                                                                        
          Affected versions of com.fasterxml.woodstox:woodstox-core are vulnerable to Out-of-bounds Write /
          Stack-based Buffer Overflow.                                                              
                                                                                                    
           ▶▶┆ Fixed for com.fasterxml.woodstox:woodstox-core at versions: 5.4.0, 6.4.0
            2┆ +- com.fasterxml.woodstox:woodstox-core:jar:6.0.3.rhlw-00001:compile

     ❱ org.springframework:spring-core - CVE-2026-41848
          Severity: LOW
          Affected versions of org.springframework:spring-core are vulnerable to Inefficient Regular
          Expression Complexity.

           ▶▶┆ Fixed for org.springframework:spring-core at versions: 6.2.19, 7.0.8
           10┆ \- org.springframework:spring-core:jar:5.3.18.rhlw-00003:compile


┌─────────────────────────────────────┐
│ 3 Unreachable Supply Chain Findings │
└─────────────────────────────────────┘

    maven_dep_tree.txt
    ❯❱ com.jayway.jsonpath:json-path - CVE-2023-51074
          Severity: MODERATE                                                                        
          Affected versions of com.jayway.jsonpath:json-path are vulnerable to Out-Of-Bounds Write. The
          vulnerability lies in the deprecated function `Criteria.parse()`, which is susceptible to a stack
          overflow.                                                                                 
                                                                                                    
           ▶▶┆ Fixed for com.jayway.jsonpath:json-path at version: 2.9.0
            4┆ +- com.jayway.jsonpath:json-path:jar:2.8.0.rhlw-00001:compile

   ❯❯❱ org.json:json - CVE-2022-45688
          Severity: HIGH                                                                            
          Affected versions of cn.hutool:hutool-json and org.json:json are vulnerable to Out-Of-Bounds Write.
          The vulnerability in the XML.toJSONObject enables attackers to initiate a Denial of Service (DoS)
          attack by exploiting crafted JSON or XML data.                                            
                                                                                                    
           ▶▶┆ Fixed for org.json:json at version: 20230227
            9┆ +- org.json:json:jar:20220320.0.0.rhlw-00003:compile

   ❯❯❱ org.springframework:spring-core - CVE-2025-41249
          Severity: HIGH                                                                            
          Affected versions of org.springframework:spring-core are vulnerable to Improper Authorization /
          Incorrect Authorization. Spring's annotation resolution can fail on methods in type hierarchies with
          parameterized super-types and unbounded generics, causing annotations used for authorization (e.g.
          @PreAuthorize, @Secured, @Transactional) to be invisible on concrete implementations. In apps using
          Spring Security's @EnableMethodSecurity, security annotations declared on generic superclasses or
          interfaces may be ignored on subclasses, potentially allowing unauthorized access.        
                                                                                                    
           ▶▶┆ Fixed for org.springframework:spring-core at version: 6.2.11
           10┆ \- org.springframework:spring-core:jar:5.3.18.rhlw-00003:compile



┌──────────────┐
│ Scan Summary │
└──────────────┘
✅ CI scan completed successfully.
 • Findings: 6 (0 blocking)
 • Rules run: 135980
 • Targets scanned: 2
 • Parsed lines: ~100.0%
 • Scan was limited to files tracked by git
 • For a detailed list of skipped files and lines, run semgrep with the --verbose flag

```