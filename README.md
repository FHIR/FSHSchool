# FSH School

The FSH School website is built using [Hugo](https://themes.gohugo.io/hugo-theme-learn/) and
deployed to [http://fshschool.org/](http://fshschool.org/) via GitHub Actions that build the site
and push its contents to the [gh-pages](https://github.com/FHIR/FSHSchool/tree/gh-pages) branch.

## FHIR Foundation Project Statement

- Maintainers: This project is maintained by the HL7 community.
- Issues / Discussion: For FSH School website issues, such as bug reports, comments, suggestions, questions, and feature requests, visit [FSH School website GitHub Issues](https://github.com/FHIR/FSHSchool/issues). For discussion of FHIR Shorthand and its associated projects, visit the FHIR Community Chat @ https://chat.fhir.org. The [#shorthand stream](https://chat.fhir.org/#narrow/stream/215610-shorthand) is used for all FHIR Shorthand questions and discussion.
- License: All contributions to this project will be released under the Apache 2.0 License, and a copy of this license can be found in [LICENSE](LICENSE).
- Contribution Policy: The FSH School website Contribution Policy can be found in [CONTRIBUTING.md](CONTRIBUTING.md).
- Security Information: The FSH School website Security Information can be found in [SECURITY.md](SECURITY.md).
- Compliance Information: N/A.

## About FSH School

To learn more about FSH School, watch the [Community Engagement, Issue Tracking, and Documentation Knowledge Sharing Session](https://vimeo.com/990594107/c35c3f5e43) and view the [presentation slides](https://confluence.hl7.org/display/FHIR/FSH+Knowledge+Sharing+Sessions?preview=/256509612/256185999/KSS%202%20-%20FSH%20Community%20Engagement.pdf). This session covers what type of information is documented in FSH School and how to update the documentation.

# Local Development

To develop this site locally:

1. Clone this repository
   ```bash
   $ git clone git@github.com:FHIR/FSHSchool.git
   ```
   and add submodules
   ```bash
   $ git submodule update --init --recursive
   ```
2. [Install Hugo](https://gohugo.io/getting-started/installing/)
3. Add and edit content, referring to the [Hugo documentation](https://gohugo.io/documentation/)
   as needed
4. Run the Hugo server
   ```bash
   $ hugo server
   ```
   _NOTE: Optionally use the `-D` flag to include draft content_
5. View the local site in your browser at [http://localhost:1313/](http://localhost:1313/)

# Local Build

To do a local build (i.e., build the static site files without serving them), you will additionally
need to install the `postcss-cli` and `autoprefixer` node modules. You can install them locally
or globally:
```bash
$ npm install postcss-cli autoprefixer
```
or
```bash
$ npm -g install postcss-cli autoprefixer
```

Then run hugo with no arguments:
```bash
$ hugo
```

This will build the static site files to the `./public` folder. They can then be served using any
standard HTTP server.

# Tutorial files

Files for the FSH tutorial are in the `./fsh-tutorial` folder. If you update any of these files, you should also rebuild the zipped version of the tutorial by running:
```bash
$ npm run zip-tutorial
```

This is also run automatically during the deployment process.

# License

Copyright 2020+ Health Level Seven International

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
