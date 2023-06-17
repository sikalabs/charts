<p align="center">
  <h1 align="center">sikalabs/charts</h1>
  <p align="center">
    <a href="https://sikalabs.com"><img alt="SikaLabs" src="https://img.shields.io/badge/OPENSOURCE BY-SIKALABS-131480?style=for-the-badge"></a>
    <a href="https://sikalabs.com"><img alt="SikaLabs" src="https://img.shields.io/badge/-sikalabs.com-gray?style=for-the-badge"></a>
    <a href="mailto://opensource@sikalabs.com"><img alt="SikaLabs" src="https://img.shields.io/badge/-opensource@sikalabs.com-gray?style=for-the-badge"></a>
  </p>
</p>

### How to release chart
1. `git clone sikalabs/charts`
<br><br>
2. `make version-bump NAME=<chart-name-to-release> VERSION=<chart-version-to-release>`
<br><br>
3. `make release NAME=<chart-name-to-release>`
<br><br>
4. `make version-bump NAME=<chart-name-to-release> VERSION=<future-chart-version>-dev`
<br><br>
5. `git push`
<br><br>
6. `git clone sikalabs/helm-repo-sikalabs`
<br><br>
7. copy released chart `.tgz` to `sikalabs/helm-repo-sikalabs/repo/`
<br><br>
8. `make regenerate`
<br><br>
9. `git push`
