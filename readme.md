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

### Trivy for POM with CVE
```
$ cp pom-with-cve.xml pom.xml

$ trivy -v
Version: dev
Vulnerability DB:
  Version: 2
  UpdatedAt: 2026-09-23 07:12:28.724391795 +0000 UTC
  NextUpdate: 2026-09-24 07:12:28.724391614 +0000 UTC
  DownloadedAt: 2026-09-23 08:36:29.494641973 +0000 UTC

$ trivy --vex remediation.openvex fs .
.....
pom.xml (pom)

Total: 6 (UNKNOWN: 0, LOW: 1, MEDIUM: 2, HIGH: 3, CRITICAL: 0)

┌──────────────────────────────────────┬────────────────┬──────────┬────────┬───────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│               Library                │ Vulnerability  │ Severity │ Status │ Installed Version │ Fixed Version │                            Title                             │
├──────────────────────────────────────┼────────────────┼──────────┼────────┼───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ com.fasterxml.woodstox:woodstox-core │ CVE-2022-40152 │ MEDIUM   │ fixed  │ 6.0.3             │ 6.4.0, 5.4.0  │ woodstox-core: woodstox to serialise XML data was vulnerable │
│                                      │                │          │        │                   │               │ to Denial of Service...                                      │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2022-40152                   │
├──────────────────────────────────────┼────────────────┤          │        ├───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ com.jayway.jsonpath:json-path        │ CVE-2023-51074 │          │        │ 2.8.0             │ 2.9.0         │ json-path: stack-based buffer overflow in Criteria.parse     │
│                                      │                │          │        │                   │               │ method                                                       │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2023-51074                   │
├──────────────────────────────────────┼────────────────┼──────────┤        ├───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ org.json:json                        │ CVE-2022-45688 │ HIGH     │        │ 20220320          │ 20230227      │ json stack overflow vulnerability                            │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2022-45688                   │
│                                      ├────────────────┤          │        │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
│                                      │ CVE-2023-5072  │          │        │                   │ 20231013      │ JSON-java: parser confusion leads to OOM                     │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2023-5072                    │
├──────────────────────────────────────┼────────────────┤          │        ├───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ org.springframework:spring-core      │ CVE-2025-41249 │          │        │ 5.3.18            │ 6.2.11        │ org.springframework/spring-core: Spring Framework Annotation │
│                                      │                │          │        │                   │               │ Detection Vulnerability                                      │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-41249                   │
│                                      ├────────────────┼──────────┤        │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
│                                      │ CVE-2026-41848 │ LOW      │        │                   │ 7.0.8, 6.2.19 │ spring-framework: Spring Framework: Regular Expression       │
│                                      │                │          │        │                   │               │ Denial of Service in AntPathMatcher                          │
│                                      │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2026-41848                   │
└──────────────────────────────────────┴────────────────┴──────────┴────────┴───────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘

```

### Trivy for POM with Remediated CVE
```
$ cp pom-with-cve.xml pom.xml

$ trivy -v
Version: dev
Vulnerability DB:
  Version: 2
  UpdatedAt: 2026-09-23 07:12:28.724391795 +0000 UTC
  NextUpdate: 2026-09-24 07:12:28.724391614 +0000 UTC
  DownloadedAt: 2026-09-23 08:36:29.494641973 +0000 UTC

$ trivy --vex remediation.openvex fs .
.....
pom.xml (pom)

Report Summary

┌─────────┬──────┬─────────────────┬─────────┐
│ Target  │ Type │ Vulnerabilities │ Secrets │
├─────────┼──────┼─────────────────┼─────────┤
│ pom.xml │ pom  │        0        │    -    │
└─────────┴──────┴─────────────────┴─────────┘

```