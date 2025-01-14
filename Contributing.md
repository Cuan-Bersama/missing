
# Contribute to missing.css


## Issues

PRs are not the only form of contributing. Reporting issues is also important
and helps a lot. And, like PRs, issues that don't match the project's goals or
standards of quality will not be accepted.

Don't be afraid! We take it upon ourselves to help people contribute. You might
get follow-up questions or asked for more information when you submit an issue.
It's not productive to close issues just because we don't like them.

For feature requests, prefer [Discussions][] instead of Issues, unless the
feature is directly in our [RFP][] or [Concepts][] pages. Otherwise, once a
feature is agreed upon and there is interest in implementing it, issues can be
created.

missing.css is developed on a "free to use, pay to demand" basis. Contributors
are not beholden to users and can work on whatever they like. If you need a
specific feature or bugfix for your business, consider putting up a bounty.
(Not recommended: Making an unsolicited donation and then demanding specific
work. That's not how you buy something).

[RFP]: https://missing.style/rfp/
[Concepts]: https://missing.style/concepts/
[Discussions]: https://github.com/bigskysoftware/missing/discussions


## Development Environment

[Deno] is needed to build the project.

  * Build just the CSS:
      ~~~ sh
      deno task css
      ~~~

  * Build the website, which also builds the CSS and creates the version
    archive:
      ~~~ sh
      deno task prod
      ~~~

  * Start a development server, which will watch the code and automatically
    refresh in the browser:
      ~~~ sh
      deno task dev
      ~~~

[Deno]: https://deno.land/


## Branching

You'll notice that we don't have a `master` or `main` branch. Instead, the 
`dev` branch is the default. This way, the default place to open a pull request
is the right one.

We use git tags for releases. Any tag matching the regex `^v\d+\.\d+\.\d+` is a
release (this regex is used by the script `build/version-archive.sh`). After
creating a tag, move the `prod` branch to it.

That's it! We don't expect to use feature branches that often, instead working
directly on `dev` because CSS is hard to merge.


## Publishing a release

To publish a new release:

  * Make sure there is a changelog in `www/releases/`:

      ~~~ sh
      cat >www/releases/4.2.0.md <<EOF
      ---
      release: 4.2.0
      artifacts:
        Missing.css:
          Plain: missing.css
          Minified: missing.min.css
          Min. + Gzipped: missing.min.css.gz
          Min. + Brotlied: missing.min.css.br
        Missing Prism:
          Plain: missing-prism.css
          Minified: missing.min.css
          Min. + Gzipped: missing.min.css.gz
          Min. + Brotlied: missing.min.css.br
      ---

      # Changelog

       - **Breaking:**{.color.warn} Removed everything
      EOF
      ~~~

  * Create a git tag matching the regex `^v\d+\.\d+\.\d+` (i.e.: `v1.2.2`,
    `v1.3.3-special-build-1`, NOT `1.1.2`, `v3`, `v1.2-alpha`). It's important
    that the git tag has the correct format since we use the regex above in
    code.

      ~~~ sh
      git tag v4.2.0
      ~~~
  
  * Move the `prod` branch to that tag:

      ~~~ sh
      git branch -f prod v4.2.0
      ~~~
  
  * Push:

      ~~~ sh
      git push --tags origin prod
      ~~~
  


## Dev Notes

You can note your thoughts and leave notes for maintainers in the `dev-notes`
file. The structure is:

  ~~~
  YYYY-MM-DD

  Your notes here

  -- your-github-username
  ~~~

Leave two empty lines between notes.


## Project Structure

  * `src/` -- the CSS source code. This is processed with PostCSS (see build/).
      * `main.css` -- definitions used project-wide.
      * `syntax.css` -- a [Prism] syntax theme
      * `core/`
        * `sanitize.css` -- a CSS normalize
      * `elements/` -- HTML elements, organized by sections of the [HTML spec][]
          * `sections.css` -- 4.1 The document element, 4.3 Sections
          * `grouping.css` -- 4.4 Grouping content
          * `inline.css` -- 4.5 Text-level semantics, 4.7 Edits
          * `embedded.css` -- 4.8 Embedded content
          * `tabular.css` -- 4.9 Tabular data
          * `forms.css` -- 4.10 Forms
          * `interactive.css` -- 4.11 Interactive elements
      * `components/` -- components. Each component can be used as
        a class (`<div class="my-component">`),an attribute 
        (`<div my-component>`) or a custom tag (`<my-component>`).

        Each component should have a documentation comment, explaining its
        purpose, appearance and usage.
      * `util` -- utility classes, incl. layout and colorways.
          * `colorway.css` -- colorway definitions and tools for using them.
          * `density.css` -- utilities for setting density
          * `generic.css` -- utilities many sites need, e.g. visually hidden
          * `layout.css` -- layout tools
          * `typographic.css` -- typesetting tools

  * `www/` -- the project website, built with [Lume]
      * `demos/` -- demo pages. These should have a `name` specified in the
        frontmatter. They will be listed in the demo page (`../demos.md`).
      * `releases/` -- release notes.

  * `build/` -- buildscripts
      * `postcss.ts` -- builds the CSS.
      * `version-archive.sh` -- builds all past releases into the dist folder.
        The script _should_ run on any POSIX environment with npm and git
        installed, though it hasn't been tested much.

  * `dist/` -- build results. This is also where the built website is.
      * `missing.css` -- unminified dev build
      * `missing.min.css` -- minified
      * `missing.min.css.br` -- minified + gzip compressed
      * `missing.min.css.gz` -- minified + brotli compressed
      * `missing-prism.css` (& `.min.css`, etc.) -- [Prism] theme
      * `archive` -- past releases of the above

[Lume]: https://lume.land
[Prism]: https://prismjs.com
[HTML spec]: https://html.spec.whatwg.org/multipage/#toc-semantics