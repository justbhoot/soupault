# 4.0.1

## Bug fixes

* Pages that produce completely empty index entries (no defined fields) no longer cause an internal error (github#45, report by github/@akavel).
* With `clean_urls = false`, pages now have correct url index fields (with original extension removed) (github#44, report by github/@laumann).

# 4.0.0

## Breaking changes

* Index views no longer have default rendering and require `index_template`, `index_item_template`, or `index_processor`.
* `absolute_links` widgets without `prefix` are considered configuration errors.
* Empty `settings.build_dir` is no longer allowed (formerly caused soupault to use the current dir as output dir).

## New features

* Index fields can now be marked as required (missing fields in pages will cause build errors).
* New `settings.process_pages_first` option allows the user to force processing certain pages before all other.
* Lua code can now take over certain tasks via page processing hooks: `pre-parse`, `pre-process`, `post-index`, `render`, and `save`.
* Index processors can now be written in Lua.
* Lua index processors can inject new pages into the queue (for pagination, taxonomies, and so on).
* New `index.index_first` option allows pages to access their own index entries (at cost of a two-pass workflow with a reduced index-only run done first).
* Index sorting options (`sort_by` and `sort_type`) can be specified in every index view independently.
* Index views now support `action` option to control generated HTML insertion.
* Lua plugins code can now be inlinded in the config using `lua_source` option.
* `relative_links` and `absolute_links` widgets support almost all imaginable that can contain URLs in attributes.
* New `--dump-index-json <file>` option allows exporting metadata to JSON without setting `index.dump_json` in the config.

## New Lua plugin API functions and variables

### New variables

* `target_file` (path to the output file, relative to the current working directory).
* `index_entry` (the index entry of the page being processed if `index.index_first = true`, otherwise it's `nil`).

### New functions

* `String.slugify_soft(string)` replaces all whitespace with hyphens, but doesn't touch any other characters.
* `String.url_encode(str)` and `String.url_decode(str)` for working with percent-encoded URLs.
* `String.join_url` as an alias for `String.join_path_unix`.
* `HTML.to_string(etree)` and `HTML.pretty_print(etree)` return string representations of element trees, useful for save hooks.
* `HTML.create_document()` creates an empty element tree.
* `HTML.clone_document(etree)` make a copy of a complete element tree.
* `HTML.append_root(etree, node)` adds a node after the last element.
* `HTML.child_count(elem)` returns the number of children of an element.
* `HTML.unwrap(elem)` yanks the child elements out of a parent and inserts them in its former place.
* `Table.take(table, limit)` removes up to `limit` items from a table and returns them.
* `Table.chunks(table, size)` splits a table into chunks of up to `size` items.
* `Table.has_value(table, value)` returns true if `value` is present in `table`.
* `Table.apply_to_values(func, table)` applies function `func` to every value in `table` (a simpler version of `Table.apply` if you don't care about keys).
* `Table.get_nested(table, {"key", "sub-key", ...})` and `Table.get_nested_default(table, {"key", "sub-key", ...}, default)` for easily retrieving values from nested tables (handy for getting config options).
* `Table.keys(table)` returns a list of all keys in `table`.
* `Sys.list_dir(path)` returns a list of all files in `path`.
* `Value.repr(value)` returns a string representation of a Lua value for debug output (similar to Python's `__repr__` in spirit).

### Unicode string functions

* `String.length` is now Unicode-aware, the old ASCII-only version is now available as `String.length_ascii`
* `String.truncate` is now Unicode-aware, the old ASCII-only version is now available as `String.truncate_ascii`

## Bug fixes

* The `numeric` index entry sorting method works correctly again.
* The `include_subsections` option is now available to all widgets and documented (it makes `section` apply to a dir and all its subdirs).
* Nested inline tables in `soupault.toml` work correctly now (fixed in [OTOML 1.0.1](https://github.com/dmbaturin/otoml/releases/tag/1.0.1)).
* Fixed an unhandled exception when handling misconfigured index views.
* Fixed a possible unhandled exception during page processing.
* `Sys.list_dir` correctly handles errors when the argument is not a directory.

## Behaviour changes

* If `index.sort_by` is not set, entries are now sorted by their `url` field rather than displayed in arbitrary order.
* Newlines are now inserted after HTML doctype declarations.
* Index entries are no longer sorted twice, so performance may be marginally better.
* The ToC widget now logs ignored headings in debug mode, to make it easier to debug configuration issues.
* Internal errors now produce a message that tells the user how to enable debug output and get an exception trace, if it's not enabled. I hope this feature goes unused. ;)
* Preprocessor commands are now quoted in debug logs for better readability (e.g. `running preprocessor "cmark --unsafe --smart"...`).
* Many debug log messages were improved and clarified.

# 3.2.0

## New features

* New `persistent_data` variable allows storing data between Lua plugin runs.
* New `HTML.matches_selector` and `HTML.matches_any_of_selectors` plugin functions for checking if elements match given selectors.
* New `settings.soupault_version` option for setting _minimum_ required soupault version.

## Bug fixes

* Pages with spaces and special characters inside file paths are now handled correctly by preprocessors.

# 3.1.0

## New features

* New `Sys.join_path_unix`, `Sys.basename_unix`, and `Sys.dirname_unix` plugin functions for safely working with URLs on all OSes.
* New `Table.iter_ordered` and `Table.iter_values_ordered` functions for ordered iteration over integer-indexed tables.
* New `ignore_heading_selectors` in the ToC widget for excluding certain headings from the ToC.

# 3.0.0

## Breaking changes

* Older versions allowed newlines in inline TOML tables, which is against the TOML standard. Since 3.0.0, they are no longer allowed.

## New features

* On UNIX platforms, log messages are now color-coded by severity. Coloring can be disabled with `NO_COLOR` environment variable.
* New `TOML.from_string/TOML.from_string_unsafe` and `YAML.from_string/YAML.from_string_unsafe` for TOML and YAML parsing.

## Misc

Soupault now uses [OTOML](https://opam.ocaml.org/packages/otoml) instead of To.ml for TOML parsing and manipulation.


# 2.8.0

## New features

* New `--show-default-config` and `--show-effective-config` options for displaying
* Default settings are now available to plugin in `config` variable.

## Bug fixes

* `include_subsections` option in index views now works correctly (report by toastal).
* The default config generated by `soupault --init` now includes all options, including `pretty_print_html`, `plugin_discovery`, and `plugin_dirs` (report by Crystal-RainSlide). 

# 2.7.0

## New features

* New `wrap` widget.
* Support for specifying multiple build profiles, e.g., `soupault --profile live --profile debug`.
* Any widget can now be disabled by adding `disabled = true` to its config.

# 2.6.0

## New features

* New `index.sort_type` option for sorting index entries in different ways (can be `calendar`, `numeric`, and `lexicographic`, the default is `calendar`).
* New `index.strict_sort` option that makes soupault fail the build if a value can't be parsed.

## Bug fixes

* Fixed empty page output in a situation when the config has `generator_mode = false` and `keep_doctype = false`, and the page lacks an `<HTML>` element.
* Malformed dates no longer cause soupault to crash (that could happen due to incomplete exception handling).

# 2.5.0

## New features

* New `settings.pretty_print_html` option for choosing whether to pretty-print output HTML or not.
* New widgets for internal link rewriting: `relative_links` and `absolute_links`.
* New plugin functions: `Sys.get_extensions` and `Sys.has_extension`.

## Bug fixes

* External program errors in `exec` and `preprocess_elements` can no longer crash soupault on Windows.
* `ignore_extensions` option now checks all extensions rather than just the last, i.e., `ignore_extensions = ["tar"]` will match both `file.tar` and `file.tar.gz` now (report by Anton Bachin).

## Misc

* `settings.debug = true` and `--debug` automatically enable exception traces now.
* Better error messages for attempts to run soupault outside of a project directory, and for missing templates and site_dir.
* Proper alignment of options in the output of `soupault --help` (patch by Anton Bachin).

# 2.4.0

## New features

* New `keep = true` in the title widget allows keeping existing title.
* New `index.leaf_file` allows marking subdirectories as "hand-made clean URLs".
* New `index.force_indexing_path_regex` option for treating some index pages as normal pages.

### New plugin functions

* `widget_config` alias for `config` (since now there’s the global `soupault_config`, a more specific alias may be a good idea)
* `build_dir` and `site_dir` variables in the plugin environment (PR by [Hristos](https://hristos.lol))
* `Table.get_key_default(table, key, default_value)`
* Type checking functions in the new `Value` module: `is_int`, `is_float`, `is_string`, `is_table`, `is_list` (table with all integer keys), `is_nil`.
* `Sys.mkdir`, `Sys.get_file_modification_date`

## Bug fixes

* `include_subsection` option in the `[index]` table works correctly now (used to cause a spurious option validation error).
* soupault no longer outputs duplicate newlines on Windows (#19, reported by [wilt00](https://github.com/wilt00)).
* The `HTML.get_heading_level` function now works with nodes returned by `HTML.select` (rather than just values created with `HTML.create_element`).

# 2.3.0

## New features

* TOML type errors now include expected and actual type for ease of debugging, like "expected a string but found a table".
* `insert_html` widget now supports `parse = false`, mainly for consistency with `include` and `preprocess_element`.

### New plugin functions

* `String.base64_encode` and `String.base64_decode`.
* `Sys.basename` and `Sys.dirname`.
* `Sys.get_extension` (e.g. `"hello.jpg" → "jpg"`).
* `Sys.file_exists`.
* `Sys.is_file` and `Sys.is_dir`. Both return `nil` if it does not exist.
* `Sys.run_program_get_exit_code`. Returns the exit code unlike, `Sys.run_program`. 0 means success.
* `Sys.delete_file` and `Sys.delete_recursive`

## Bug fixes

* The `profile` option for widgets works as expected again.

# 2.2.0 

## New features

* New `custom_options` config table for storing user/plugin-defined options.
* New `soupault_config` variable allows plugins to access the global soupault config.
* New `JSON.unsafe_from_string()` function that returns `nil` if it cannot parse its input.
* Lua-ML supports the modulo operator now (`%`).
* The `escape` filter of the template processor now emits symbolic HTML entities like `&gt;` now, rather than numeric character codes.

#### Table helpers

Iterating through tables is a real weak point of the 2.5 era Lua implemented by Lua-ML.
To make working with tables simpler, I’ve added a high level few helpers functions, including:

* `Table.iter(func, table)` 
* `Table.iter_values(func, table)`
* `Table.apply(func, table)`
* `Table.fold(func, table)`
* `Table.fold_values(func, table)`

## Bug fixes

* Fixed a TOML syntax error in default configs generated by `soupault --init`.
* Integer-indexed Lua tables are now correctly handled as "lists" when projected back to OCaml code.
* The time part of datetime values is no longer ignored in index entry sorting.

## Platform support

32-bit Windows is no longer supported, all future Windows builds will be for 64-bit Windows only.

# 2.1.0

## New features

* New `default_content_action` and `default_content_selector` options for the default template.
* New `content_action` and `content_selector` options for custom templates.
* New slugification options in the ToC widget: `slug_regex`, `slug_replacement_string`, and `slug_force_lowercase`.
* New `max_heading_link_level` level in the ToC widget allows adding heading links to headings too deep for the ToC itself.
* New `settings.keep_doctype` option allows preserving existing doctype if it's present.

## Bug fixes

* Soupault no longer erroneously creates directories or copies asset files when run with `--index-only`.
* Presence of an invalid config file in the working directory no longer prevents you from running `soupault --version`.
* Better error reporting for TOML type errors in string values.

# 2.0.0

## Breaking changes

* There is no built-in content model anymore, all index fields must be defined in the config.
* The built-in template processor is no Jingoo rather than Mustache.
* The `breadcrumb_template` in the breadcrumbs widgets is now a Jingoo template.

There is an [online config convertor](https://soupault.app/1-to-2) to simplify migration.

The reason for removing the built-in content model was that custom fields introduced in soupault ...
were already more flexible than the old-built in fields: they had `extract_attribute` and `default_value` options.
Old field configuration, however, were limited to just a selector and nothing else,
since their syntax wasn't designed to be extensible: `index_date_selector`, `index_author_selector`,
`index_title_selector`, and `index_excerpt_selector`.

That was a design mistake that was impossible to fix without completely changing the syntax.
Removing the inflexible built-in fields in the process looked like a logical step.

## New features

* Support for multiple page templates.
* Lua plugins have access to the metadata through a new `site_index` variable.
* New `String.render_template(template_string, env_table)` plugin function for Jingoo template rendering.
* New `JSON.to_string` and `JSON.pretty_print` functions for JSON output.
* New `Sys.get_file_size` function.
* New `min_headings` option in the ToC widget to show a ToC only for pages with enough headings.

# 1.13.0

## New features

* New `valid_html` option in the ToC widget that makes it output nested lists inside a `<li>` rather than directly under `<ul>`/`<ol>`.
* New `HTML.get_headings_tree` function for getting page ToC data structure and rendering a custom ToC.
* New `String.to_number` function for parsing numbers.
* Any values can be passed to plugins from the TOML config, not only strings.
* New `force` option in the `title` widget forces it to create a `<title>` element if a page doesn't have it.

# 1.12.0

* Proper error reporting for widget dependency problems.
* Plugin API functions for HTML element handling now accept `nil` values and return `nil` in that case.

# 1.11.0

## New features

* Support for multiple page templates.
* New `extract_attribute` option in custom index fields allows extracting element attributes rather than content.
* New plugin functions for HTML manipulation: `HTML.set_attribute`, `HTML.is_element`.
* New `String.join(strings, separator)` function.

# 1.10.0

## New features

* Lua plugin files can now be discovered automatically if you specify `settings.plugin_discovery = true`. The default plugin directory is `plugins/`.
* New plugin functions for HTML manipulation: `HTML.get_tag_name`, `HTML.select_any_of`, `HTML.select_all_of`.
* New plugin functions for checking what OS soupault is running on: `Sys.is_unix`. and `Sys.is_windows`.
* New `page_file` field in page index entries.

## Bug fixes

* Files without extensions in the site dir no longer cause soupault to crash with an exception.

# 1.9.0

## New features

* New `--index-only` option to only extract site index and exit without generating pages.
* Index extraction can now be limited to certain pages or sections.
* `preprocess_element` widget now allows lists of selectors in the `selector` option.
* New plugin functions for executing external programs: `Sys.run_program` and `Sys.get_program_output`.

## Bug fixes

* The `title` widget correctly removes all HTML tags from the title string (if there are any) and doesn't add extra whitespace (thanks to Thomas Lethan).
* CSS selector syntax errors are now handled gracefully.

# 1.8.0

## New features

* It's now possible to limit widgets to "build profiles": a widget with `profile = "live"` will only be processed if you run `soupault --profile live`. 
* `preprocess_element` and `exec` widget can now access the target directory setting via `TARGET_DIR` environment variable.
* New plugin functions allows to specify minimum supported soupault version: `Plugin.require_version("42.0.0")`.

### New plugin functions

* `Sys.random(max)`
* `Sys.get_program_output(command)`
* `Log.debug(message)`
* `HTML.replace_element` and `HTML.delete_element`
* `String.trim(string)` and `String.slugify_ascii(string)`

# 1.7.0

## New features

* New `preprocess_element` for piping HTML element content through external programs.
* There can be multiple index views now.
* It's now possible to specify default values for custom index fields.

## Build

Building soupault now requires OCaml 4.08 or later.

# 1.6.1

## Bug fixes

* `<noscript>` elements in the page `<body>` are now handled correctly.

# 1.6.0

## New features

* Added support for built-in index renderers (using Mustache templates).
* The ToC widget now supports `strip_tags` option.
* It's now possible to schedule index extraction to run after certain widgets are processed with `index.extract_after_widgets` option.

### New plugin functions

* `Plugin.exit` and `Plugin.fail`
* `Regex.split`
* `HTML.create_text` and `HTML.strip_tags`
* `Sys.read_file`, `Sys.join_path`
* New `page_url` variable available to plugins.
* Integers and booleans can be passed to plugins from the config, but they are converted to strings.

## Bug fixes and improvements

* Widget options `section` and `exclude_section` now behave as expected.
* The default page template now includes a charset meta tag, set to UTF-8.
* Debug messages for pages excluded by exclude_section/page/regex options now say which widget they are talking about.
* There's now a debug message telling whether page template was used or not (in generator mode only).

# 1.5.0

## New features

* New `settings.complete_page_selector` option for specifying element whose presence excludes page from templating (by default, `<html>`).
* New post-processor mode that disabled templating entirely, activated with `settings.generator_mode = false`.
* Soupault config location can be overridden with `SOUPAULT_CONFIG` environment variable.
* New `delete_element` widget for deleting HTML elements from pages.

# 1.4.0

## New features

* Separate `--verbose` and `--debug` options.
* New `settings.ignore_file_extensions` option for ignoring certain files.
* `soupault --init` generates more illustrative and well-commented configs.

# 1.3.1

## Bug fixes

* [Windows] Preprocessors and other external programs that are `%PATH%` now work as expected, no need to specify an absolute path
* `[preprocessors]` section doesn't cause an erroneus "invalid config section" warning anymore

# 1.3.0

## Improvements

* **[footnotes widget]** New options `link_id_prepend` and `back_link_id_append` that allow you to create a separate "namespace" for footnote links.
* **[footnotes widget]** More logical handling of ids: if a footnote element has an id, it's kept in place so that the footnote itself can be hotlinked using that id.
* Invalid config options cause warning messages now, e.g. `option "foo" is not valid for widget "title"`.
* There's now spellchecking and suggestions for mistyped options, e.g. `invalid option "backlinks", did you mean "back_links"?`
* [AsciiDoc](http://asciidoc.org/) extension (`.adoc`) is now in the default page extensions list.
* New Lua plugin functions for error reporting: `Log.info`, `Log.warning`, `Log.error`

## Bug fixes

* `soupault --init` generates correct `page_file_extensions` option in the default config now.
* Incorrect config options no longer pass silently.
* Useless "build directory doesn't exist" log message no longer appears when the build directory does exist.
* Sample site config works properly with recent versions.

## Build

* [ppx_monadic](https://opam.ocaml.org/packages/ppx_monadic/) library was replaced with more actively maintained [ocaml-monadic](https://opam.ocaml.org/packages/ocaml-monadic/).
* Soupault can be built with OCaml up to the latest 4.09.0.

# 1.2.0

## New features

* Lua plugin support and initial library of plugin functions.

## Bug fixes

* Better error reporting for circular and broken widget dependencies.
* Correct error message for missing "html" option of the `include_html` widget.

# 1.1.0

## New features

### Back links in footnotes

Footnotes now include links back to the original location in the document for easier navigation.
It's possible to disable this and make footnote links one way with `back_links = false` widget option.

```
[widgets.footnotes]
  widget = "footnotes"
  # No back links
  back_links = false
  # ...
```

### Path regex options for widgets

It's now possible to limit widgets to page file paths matching a Perl-compatible regex.

```
[widgets.starts-with-foo]
  path_regex = "/foo(.*)"
  widget = "insert_html"
  selector = "body"
  html = '<p>This page name starts with foo!</p>'
```

### Excluding pages from widgets

It's now possible to explicitly exclude pages from a widget using these options:
* `exclude_page`
* `exclude_section`
* `exclude_path_regex`

For example:
```
[widgets.toc]
  # No ToC on any page named "index"
  exclude_path_regex = '/index(\.*)'
```

## Bug fixes

* HTML tags inside headings are now preserved by the ToC widget and the auto-indexer.
* Autoindex generator doesn't inadvertently remove nodes from the page anymore.

## Platform support

Binaries for macOS are available.

# 1.0.1

## Behaviour changes

Only files with extensions `.htm .html .md .rst` are assumed to be pages now, all other files are copied to the build dir unchanged.
This is to simplify switching for existing websites that store assets together with pages,
and to comply with the principle of least astonishment.

You can change it with `page_file_extensions` option, for example:

```
[settings]
  page_file_extensions = ["htm", "html", "md", "rst", "txt"]
```

## New features

### JSON index data export

It is now possible to export the global website index to file:

```
[index]
  index = true
  dump_json = "path/to/file.json"
```

### Custom index fields

Example:

```
[index.custom_fields]
  tags = {selector = ".tag", select_all = true}
  category = {selector = "#category" }
```

### Lists of selectors

`page` and `section` options can now be either single selector or lists.
This also applies to the `selector` option in title and footnotes widgets, with different semantics (select any that exists and select all respectively).

## Bug fixes

* Fixed missing dependency in the OPAM package spec.
* Strict mode (`--strict`/`strict = true`) now actually stops on the first error.
* Output lines are no longer prefixed with superfluous executable file name
* Correct spelling of `use_heading_text` and `use_heading_slug` options. Old `use_header_*` options are kept for compatibility but deprecated.
* A warning and a suggestion to use `soupault --init` are now displayed if neither site dir nor default template exist.
* Graceful handling of sections (directories) without an index page or with no pages at all

# 1.0.0-beta1

The first beta. It's stable enough to build my own website with a pretty interesting config, but may still have rough edges.