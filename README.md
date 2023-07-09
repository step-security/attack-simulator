# GitHub Actions Goat: Vulnerable by Design GitHub Actions Workflows

[![Maintained by stepsecurity.io](https://img.shields.io/badge/maintained%20by-stepsecurity.io-blueviolet)](https://stepsecurity.io/?utm_source=github&utm_medium=organic_oss&utm_campaign=harden-runner)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://raw.githubusercontent.com/step-security/harden-runner/main/LICENSE)

GitHub Actions Goat by [StepSecurity](stepsecurity.io) is an educational project that simulates common security mistakes that can occur in GitHub Actions workflows.

GitHub Actions Goat incorporates best practices from [GitHub's Security Hardening for GitHub Actions guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions) to showcase how these vulnerabilities can be avoided in real-world GitHub Actions workflows.

The importance of Continuous Integration/Continuous Deployment (CI/CD) security has recently been underlined by guidance from the Cybersecurity & Infrastructure Security Agency (CISA) and the National Security Agency (NSA). As per their document [Defending Continuous Integration/Continuous Delivery (CI/CD) Environments](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF), CI/CD environments have become attractive targets for malicious cyber actors (MCAs) aiming to introduce malicious code, steal intellectual property, or cause denial of service attacks against applications.

The increasing number of supply chain attacks on CI/CD environments, such as the infamous SolarWinds, Codecov, and ua-parser-js attacks, paints a vivid picture of this growing threat.

## Understanding Vulnerabilities and Solutions with GitHub Actions Goat

This project not only demonstrates vulnerabilities but also presents solutions and references to best practices for each issue. In each scenario, we demonstrate how a particular threat can be mitigated.

| Vulnerability                                                           | Solution                                                                                             | Real-World Incidents                                                                                                                                                                                                                                                                                                                                                                                                     | References                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Poisoned Workflows Exfiltrating CI/CD Secrets                           | [GitHub Actions Runtime Security](#)                                                                 | [Codecov breach](https://about.codecov.io/security-update/), [event-stream incident](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident.html), [VS Code GitHub Bug Bounty Exploit](https://www.bleepingcomputer.com/news/security/heres-how-a-researcher-broke-into-microsoft-vs-codes-github/), [Dependency confusion](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610) | [Implement network segmentation and traffic filtering](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF), [Implement endpoint detection and response (EDR) tools](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF)                                                                                                                                                                                                                                              |
| Poisoned Workflows Tampering with Source Code or Artifacts during Build | [GitHub Actions Runtime Security](#)                                                                 | [Solar Winds (SUNSPOT) breach](http://crowdstrike.com/blog/sunspot-malware-technical-analysis/), [event-stream incident](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident.html), [Embedded malware in ua-parser-js](https://github.com/advisories/GHSA-pjwm-rvh2-c87w)                                                                                                                   | [Implement endpoint detection and response (EDR) tools](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF)                                                                                                                                                                                                                                                                                                                                                                                                     |
| Secrets Stored as Plaintext in Workflow Files or Build Logs             | [Scan for secrets in workflow files and build logs](#)                                               | [Link to Real-World Incident](#)                                                                                                                                                                                                                                                                                                                                                                                         | ["Using Secrets" section in GitHub's Security Guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#using-secrets), ["Secure secrets" section in CISA/NSA document](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF)                                                                                                                                                                                                                                               |
| Overprivileged GITHUB_TOKEN Permissions                                 | [Update workflows to use least privileged GITHUB_TOKEN permissions](#)                               | [VS Code GitHub Bug Bounty Exploit](https://www.bleepingcomputer.com/news/security/heres-how-a-researcher-broke-into-microsoft-vs-codes-github/)                                                                                                                                                                                                                                                                         | ["Use credentials that are minimally scoped" in GitHub's Security Guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#using-secrets)                                                                                                                                                                                                                                                                                                                                                                           |
| Use of Long-Term CI/CD Credentials                                      | [Audit and rotate registered secrets](#), [Use OpenID Connect (OIDC) in GitHub Actions workflows](#) | [CircleCI security alert](https://circleci.com/blog/january-4-2023-security-alert/)                                                                                                                                                                                                                                                                                                                                      | ["Audit and rotate secrets" in GitHub's Security Guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#using-secrets), ["Using OpenID Connect to access cloud resources" in GitHub's Security Guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#using-openid-connect-to-access-cloud-resources), ["Minimize the use of long-term credentials" in CISA/NSA document](https://media.defense.gov/2023/Jun/28/2003249466/-1/-1/0/CSI_DEFENDING_CI_CD_ENVIRONMENTS.PDF) |
