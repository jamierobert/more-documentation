### Labeling Strategy

##### Reccomended:

```app.kubernetes.io/name=category-service``` - Deployment Name.

```app.kubernetes.io/instance={{Pod.name}}``` - Pod Name.

```app.kubernetes.io/version=semVer``` - Code version for sure, maybe we could include a ```/container-version``` container version too.

```app.kubernetes.io/component``` - Stack name

```app.kubernetes.io/parent``` - System name

```app.kubernetes.io/managed-by``` - Feature Team details.

##### I've added :

```app.info/deployed-by``` - Username.

```app.info/code-repo``` - Link to code.

```aapp.info/api-documentation``` - Link to docs - Swagger docs.

```app.info/product-documentation``` - Link to docs - Support docs.

```app.info/logging-platform-search``` - Add a link to log search.

```app.info/release-ticket``` - Release number

```app.info/release-date``` - The date of the last release to this environment.




