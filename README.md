# About

rss-gen is a `Bourne-Again SHell` script to generate `RSS feeds` in **GitLab CI/CD** pages deployment process.

## Settings

In the `rss-gen` script, there's a variable named `FILE_SUFFIXES_GENERATED_TO_HTML`. \
This variable contains `grep pattern` to match filetypes that will trigger `RSS` generation process.
> By default, `adoc\|md` is set to match markdown and asciidoctor files.

If you need to add for example `.csv` support:
1. Extend pattern to `adoc\|md\|csv`
2. Update case: `CASE ${FILE_SUFFIX} in`.
3. a) Add entry `csv)` with `FILE_TITLE` variable set (this will set document title). \
      Example when title is first column item, delmited by comma (,):
      ```shell
      FILE_TITLE=$(cat ${FILE} | cut -d , -f1)
      ```
   b)
      Set `OUTPUT_GENERATOR_CMD`. \
      Example using **pandoc** converter.
      ```shell
      pandoc -f csv -t html5 ${FILE}
      ```
## Running

It is designed to run in **GitLab CI/CD**. Configure your `.gitlab-ci.yml` => 
set variables according to your environment & system used. 
```yml
  variables:
    RSS_TARGET_DIR: public <1>
    RSS_TARGET_URL: https://johndoe.pages.io <2>
  before_script:
      # Install dependencies
      #   curl to get artifacts, unzip to extract them,
      #   asciidoctor to generate html from adoc & md files
      #   bash to run the script
    - apk update <3>
    - apk add curl unzip asciidoctor bash <4>
  script:
    - bash ./files/rss-gen <5>
```
1. Directory where files should be generated. \
For example if we have `docs/index.adoc` and we will make `public/docs/index.html` from it, we will set this variable to `public`.
2. Target web URL where pages are deployed. Link will be constructed from this variable and file path in repository. \
For example `docs/index.adoc` will have link https://johndoe.pages.io/docs/index.adoc
3. Update [Alpine Linux APK](https://wiki.alpinelinux.org/wiki/Alpine_Package_Keeper)
> For Debian Linux, the command will be `apt -yqq update`
4. Install required packages via [Alpine Linux APK](https://wiki.alpinelinux.org/wiki/Alpine_Package_Keeper)
> For Debian Linux, the command will be `apt -yqq install curl unzip asciidoctor bash`
5. Run `rss-gen` script with `bash`
