<p align="left">
  <img src="https://raw.githubusercontent.com/step-security/supply-chain-goat/main/images/Logo.png" alt="Step Security Logo" width="340">
</p>

# Attack Simulation: Use of compromised dependencies

_Estimated completion time: 10 minutes_

## Summary of past incidents

There have been multiple attacks that can be attributed to compromised dependencies. The general theme is that a widely used dependency is hijacked and code is pushed into the dependency that makes malicious outbound calls. Such an attack can affect every system that uses that particular dependency. Below is a list of some such attacks that have taken place in the recent past.

| Number | Incident                                                                                                                                                                      | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1      | [Compromise of NPM packages coa and rc](https://blog.sonatype.com/npm-hijackers-at-it-again-popular-coa-and-rc-open-source-libraries-taken-over-to-spread-malware)            | A preinstall step `"preinstall": "start /B node compile.js & node compile.js"` was added to the updated dependency. The js files launch a batch script. The batch script then makes calls to https://pastorcryptograph.at using curl and wget                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 2      | [Compromise of ua-parser-js](https://github.com/faisalman/ua-parser-js/issues/536)                                                                                            | A preinstall step `"preinstall": "start /B node preinstall.js & node preinstall.js"` was added to the updated dependency. The js files launch a batch script. The preinstall.js then launches OS specific script i.e .bat or .sh, those scripts then uses certutil.exe and wget or curl to download file from: http://159.148.186.228/download/jsextension.exe or https://citationsherbe.at/sdd.dll. Checkout: https://my.diffend.io/npm/ua-parser-js/0.7.28/0.7.29                                                                                                                                                                                                                                   |
| 3      | [The klow / klown / okhsa incident](https://github.com/cncf/tag-security/blob/main/supply-chain-security/compromises/2021/klow-klown-okhsa.md)                                | A preinstall step `"preinstall": "start /B node preinstall.js & node preinstall.js"` was added to the updated dependency. The js files launch a batch script or a shell script acc. to the OS. The batch script then makes a call to http://185.173.36.219/download/jsextension.exe using curl, wget or certutil to download a exe or an elf binary file acc. to the OS. Checkout:https://my.diffend.io/npm/klown/0.7.29                                                                                                                                                                                                                                                                              |
| 4      | [NPM reverse shells and data mining](https://github.com/cncf/tag-security/blob/main/supply-chain-security/compromises/2020/nodejs.md)                                         | npm packages (plutov-slack-client, nodetest1010, and nodetest199) execute js script to spawn a child process according to the OS (Windows and Unix-based systems). This child process establishes a reverse shell to the attacker's server using `net.connect`. npm package npmpubman executes a js file to collect info about the victim. It collects user data from the environment info provided by NodeJS “process.env”. This data is posted to a remote host(\*.net) using `http.request`. Checkout: https://www.bleepingcomputer.com/news/security/npm-nukes-nodejs-malware-opening-windows-linux-reverse-shells/                                                                               |
| 5      | [The event-stream vulnerability](https://github.com/cncf/tag-security/blob/main/supply-chain-security/compromises/2018/event_stream.md)                                       | "Dependencies": "flatmap-stream": "0.1.1". The js file looks for the victim hot wallet profiles for a mobile apps or your regular browser. It maps public keys with walled id and then send it to a server in Kuala Lumpur (hosted here: https://www.shinjiru.com.my/ or https://111.90.151.134) using `http.request`. Checkout: https://medium.com/intrinsic-blog/compromised-npm-package-event-stream-d47d08605502                                                                                                                                                                                                                                                                                  |
| 6      | [hardhat-waffle](https://medium.com/metamask/how-metamasks-latest-security-tool-could-protect-smart-contract-developers-from-theft-e12da346aa53)                              | The attacker used typo squatting method to attack the targets. Attacker registered a package name similar to a legitimate package. Legitimate Package: `@nomiclabs/hardhat-waffle` and Malicious Package: `hardhat-waffle`. So, if a target developer by mistake used wrong name while installing that package, vulnerable code would get imported. Upon installation, the package would run a `postinstall` script that uploaded the contents of package.json, /etc/hosts, /etc/passwd and Kubernetes credential files (~/.kube/config) to a remote server. Checkout: https://medium.com/metamask/how-metamasks-latest-security-tool-could-protect-smart-contract-developers-from-theft-e12da346aa53 |
| 7      | [electron-native-notify](https://github.com/cncf/tag-security/blob/2013a607229175ff02ef862fa4e53334652bf122/supply-chain-security/compromises/2019/electron-native-notify.md) | `Electron-native-notify` package was used to do a useful package attack. Attacker pushed a commit with this Package as dependency to `EasyDEX-GUI application`, which is used by `Agama wallet`. Then the attacker updated the package with malicious code. This code downloads payload from the server on wallet app launch, which then sends the wallet seed to that remote server. Checkout: https://blog.npmjs.org/post/185397814280/plot-to-steal-cryptocurrency-foiled-by-the-npm.html                                                                                                                                                                                                          |

## How does StepSecurity mitigate this threat?

StepSecurity [`Harden-Runner`](https://github.com/step-security/harden-runner) GitHub Action analyzes outbound calls made as part of a workflow. When added to a workflow that does `npm install` or `npm ci` , an outbound call should only be expected to the registry endpoint. If there is a compromised dependency and it makes outbound calls, you can become aware of it, and identify the compromised dependency before publishing your package or application.

## Tutorial

Learn how to identify malicious dependencies by analyzing their behaviour in a GitHub Actions workflow.

The [package.json](/package.json) file has a dependency called [@step-security/malware-simulator](https://www.npmjs.com/package/@step-security/malware-simulator). This package (code is [here](/malware-simulator)) is NOT actual malware; the package just makes an outbound call to www.stepsecurity.io in a `preinstall` step. It simulates the behavior of the past incidents. In this tutorial, you will identify the simulated behavior.

1. Create a fork of the repo.

2. Go to the `Actions` tab in the fork. Click the `I understand my workflows, go ahead and enable them` button.

   <img src="../images/EnableActions.png" alt="Enable Actions" width="800">

3. GitHub Action workflow files are in the `.github/workflows` folder of the repo. Browse to the [npm.yml](../.github/workflows/npm.yml) file. Edit it using the GitHub website, and add the `step-security/harden-runner` GitHub Action as the first step. Commit the changes to `main` branch.

   ```yaml
   - uses: step-security/harden-runner@v1
     with:
       egress-policy: block
       allowed-endpoints: >
         github.com:443
         registry.npmjs.org:443
   ```

   The updated file should look like this:

   ```yaml
   name: npm_ci
   on: [push, pull_request, workflow_dispatch]

   jobs:
     test:
       runs-on: ubuntu-latest

       steps:
         - uses: step-security/harden-runner@v1
           with:
             egress-policy: block
             allowed-endpoints: >
               github.com:443
               registry.npmjs.org:443
         - uses: actions/checkout@v2
         - uses: actions/setup-node@v1
           with:
             node-version: "16"
         - run: npm install
   ```

4. This change should cause the workflow to run, as it is set to run on push. Click on the `Actions` tab and then click on the workflow run.

5. You should see the blocked call as an annotation. This call was made by the [@step-security/malware-simulator](https://www.npmjs.com/package/@step-security/malware-simulator) package. When you observe such a blocked call, investigate what is making the call, as it could be a compromised dependency.

<img src="../images/OutboundCallBlockedNode.png" alt="Outbound call blocked from package" width="800">

6. Install the [Harden Runner App](https://github.com/marketplace/harden-runner-app) to get notified via email or Slack when outbound traffic is blocked.