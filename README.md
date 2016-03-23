# markdown-toc [![NPM version](https://img.shields.io/npm/v/markdown-toc.svg)](https://www.npmjs.com/package/markdown-toc) [![Build Status](https://img.shields.io/travis/jonschlinkert/markdown-toc.svg)](https://travis-ci.org/jonschlinkert/markdown-toc)

> Generate a markdown TOC (table of contents) with Remarkable.

## TOC

- [Install](#install)
- [Usage](#usage)
- [API](#api)
  * [toc.json](#tocjson)
  * [toc.insert](#tocinsert)
  * [Utility functions](#utility-functions)
- [Options](#options)
  * [options.append](#optionsappend)
  * [options.filter](#optionsfilter)
  * [options.slugify](#optionsslugify)
  * [options.bullets](#optionsbullets)
  * [options.maxdepth](#optionsmaxdepth)
  * [options.firsth1](#optionsfirsth1)
- [Related projects](#related-projects)
- [Contributing](#contributing)
- [Building docs](#building-docs)
- [Running tests](#running-tests)
- [Author](#author)
- [License](#license)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install markdown-toc --save
```

**Features**

* Returns an object with the rendered TOC (on `content`), as well as a `json` property with the raw TOC object, so you can generate your own TOC using templates or however you want
* Works with [repeated headings](https://gist.github.com/jonschlinkert/ac5d8122bfaaa394f896)
* Uses sane defaults, so no customization is necessary, but you can if you need to.
* [filter](#filter-headings) out headings you don't want
* [Improve](#titleize) the headings you do want
* Use a custom [slugify](#optionsslugify) function to change how links are created

**Safe!**

* Won't mangle markdown in code examples in gfm code blocks that other TOC generators mistake as being actual headings (this happens when markdown headings are show in _examples_, meaning they arent' actually headings that should be in the toc. Also happens with yaml and coffee-script comments, or any comments that use `#`)
* Won't mangle front-matter, or mistake front-matter properties for headings like other TOC generators

## Usage

```js
var toc = require('markdown-toc');

toc('# One\n\n# Two').content;
// Results in:
// - [One](#one)
// - [Two](#two)
```

To allow customization of the output, an object is returned with the following properties:

* `content` **{String}**: The generated table of contents. Unless you want to customize rendering, this is all you need.
* `highest` **{Number}**: The highest level heading found. This is used to adjust indentation.
* `tokens` **{Array}**: Headings tokens that can be used for custom rendering

## API

### toc.json

Object for creating a custom TOC.

```js
toc('# AAA\n## BBB\n### CCC\nfoo').json;

// results in
[ { content: 'AAA', slug: 'aaa', lvl: 1 },
  { content: 'BBB', slug: 'bbb', lvl: 2 },
  { content: 'CCC', slug: 'ccc', lvl: 3 } ]
```

### toc.insert

Insert a table of contents immediately after an _opening_ `<!!-- toc -->` code comment, or replace an existing TOC if both an _opening_ comment and a _closing_ comment (`<!!-- tocstop -->`) are found.

_(This strategy works well since code comments in markdown are hidden when viewed as HTML, like when viewing a README on GitHub README for example)._

**Example**

```markdown
<!!-- toc -->
- old toc 1
- old toc 2
- old toc 3
<!!-- tocstop -->

## abc
This is a b c.

## xyz
This is x y z.
```

Would result in something like:

```markdown
<!!-- toc -->
- [abc](#abc)
- [xyz](#xyz)
<!!-- tocstop -->

## abc
This is a b c.

## xyz
This is x y z.
```

### Utility functions

As a convenience to folks who wants to create a custom TOC, markdown-toc's internal utility methods are exposed:

```js
var toc = require('markdown-toc');
```

* `toc.bullets()`: render a bullet list from an array of tokens
* `toc.linkify()`: linking a heading `content` string
* `toc.slugify()`: slugify a heading `content` string
* `toc.strip()`: strip words or characters from a heading `content` string

**Example**

```js
var result = toc('# AAA\n## BBB\n### CCC\nfoo');
var str = '';

result.json.forEach(function(heading) {
  str += toc.linkify(heading.content);
});
```

## Options

### options.append

Append a string to the end of the TOC.

```js
toc(str, {append: '\n_(TOC generated by Verb)_'});
```

### options.filter

Type: `Function`

Default: `undefined`

Params:

* `str` **{String}** the actual heading string
* `ele` **{Objecct}** object of heading tokens
* `arr` **{Array}** all of the headings objects

**Example**

From time to time, we might get junk like this in our TOC.

```markdown
[.aaa([foo], ...) another bad heading](#-aaa--foo--------another-bad-heading)
```

Unless you like that kind of thing, you might want to filter these bad headings out.

```js
function removeJunk(str, ele, arr) {
  return str.indexOf('...') === -1;
}

var result = toc(str, {filter: removeJunk});
//=> beautiful TOC
```

### options.slugify

Type: `Function`

Default: Basic non-word character replacement.

**Example**

```js
var str = toc('# Some Article', {slugify: require('uslug')});
```

### options.bullets

Type: `String|Array`

Default: `*`

The bullet to use for each item in the generated TOC. If passed as an array (`['*', '-', '+']`), the bullet point strings will be used based on the header depth.

### options.maxdepth

Type: `Number`

Default: `6`

Use headings whose depth is at most maxdepth.

### options.firsth1

Type: `Boolean`

Default: `true`

Exclude the first h1-level heading in a file. For example, this prevents the first heading in a README from showing up in the TOC.

## Related projects

* [gfm-code-blocks](https://www.npmjs.com/package/gfm-code-blocks): Extract gfm (GitHub Flavored Markdown) fenced code blocks from a string. | [homepage](https://github.com/jonschlinkert/gfm-code-blocks)
* [markdown-link](https://www.npmjs.com/package/markdown-link): Micro util for generating a single markdown link. | [homepage](https://github.com/jonschlinkert/markdown-link)
* [markdown-utils](https://www.npmjs.com/package/markdown-utils): Micro-utils for creating markdown snippets. | [homepage](https://github.com/jonschlinkert/markdown-utils)
* [pretty-remarkable](https://www.npmjs.com/package/pretty-remarkable): Plugin for prettifying markdown with Remarkable using custom renderer rules. | [homepage](https://github.com/jonschlinkert/pretty-remarkable)
* [remarkable](https://www.npmjs.com/package/remarkable): Markdown parser, done right. 100% Commonmark support, extensions, syntax plugins, high speed - all in… [more](https://www.npmjs.com/package/remarkable) | [homepage](https://github.com/jonschlinkert/remarkable)

## Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](https://github.com/jonschlinkert/markdown-toc/issues/new).

## Building docs

Generate readme and API documentation with [verb](https://github.com/verbose/verb):

```sh
$ npm install verb && npm run docs
```

Or, if [verb](https://github.com/verbose/verb) is installed globally:

```sh
$ verb
```

## Running tests

Install dev dependencies:

```sh
$ npm install -d && npm test
```

## Author

**Jon Schlinkert**

* [github/jonschlinkert](https://github.com/jonschlinkert)
* [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

## License

Copyright © 2016 [Jon Schlinkert](https://github.com/jonschlinkert)
Released under the [MIT license](https://github.com/jonschlinkert/markdown-toc/blob/master/LICENSE).

***

_This file was generated by [verb](https://github.com/verbose/verb), v0.9.0, on March 22, 2016._