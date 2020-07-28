# FSH School

The FSH School website is built using [Hugo](https://themes.gohugo.io/hugo-theme-learn/) and
deployed to [http://fshschool.org/](http://fshschool.org/) and [https://fshschool.github.io/](https://fshschool.github.io/) via GitHub Actions that
build the site and push its contents to the
[FSHSchool.github.io](https://github.com/FSHSchool/FSHSchool.github.io) repository.

# Local Development

To develop this site locally:

1. Clone this repository
   ```bash
   $ git clone git@github.com:FSHSchool/site.git
   ```
   and add submodules
   ```bash
   $ git submodule update --init --recursive
   ```
2. [Install Hugo](https://gohugo.io/getting-started/installing/)
3. Add and edit content, refferring to the [Hugo documentation](https://gohugo.io/documentation/)
   as needed
4. Run the Hugo server
   ```bash
   $ hugo server
   ```
   _NOTE: Optionally use the `-D` flag to include draft content_
5. View the local site in your browser at [http://localhost:1313/](http://localhost:1313/)

# License

Copyright 2020+ The MITRE Corporation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
