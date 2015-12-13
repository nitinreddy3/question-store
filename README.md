# question-store [![NPM version](https://img.shields.io/npm/v/question-store.svg)](https://www.npmjs.com/package/question-store)

> Ask questions, persist the answers. Basic support for i18n and storing answers based on current working directory.

Similar to [question-cache](https://github.com/jonschlinkert/question-cache), but persists answers to disk and supports locales and storing answers based on current working directory.

- [Install](#install)
- [Usage](#usage)
- [API](#api)
  * [Questions](#questions)
  * [Question](#question)
- [Related projects](#related-projects)
- [Running tests](#running-tests)
- [Contributing](#contributing)
- [Author](#author)
- [License](#license)

## Install

Install with [npm](https://www.npmjs.com/)

```sh
$ npm i question-store --save
```

## Usage

```js
var Questions = require('question-store');
```

## API

### Questions

### [Questions](index.js#L27)

Create an instance of `Questions` with the given `options`.

**Params**

* `options` **{Object}**: question store options

**Example**

```js
var Questions = new Questions(options);
```

### [.set](index.js#L82)

Cache a question to be asked at a later point. Creates an instance of [Question](#question), so any `Question` options or settings may be used.

**Params**

* `value` **{Object|String}**: Question object, message (string), or options object.
* `locale` **{String}**: Optionally pass the locale to use, otherwise the default locale is used.

**Example**

```js
questions.set('drink', 'What is your favorite beverage?');
// or
questions.set('drink', {
  type: 'input',
  message: 'What is your favorite beverage?'
});
// or
questions.set({
  name: 'drink'
  type: 'input',
  message: 'What is your favorite beverage?'
});
```

### [.setDefault](index.js#L100)

Add a question that will have its answer stored as a default value.

**Params**

* `name` **{String}**
* `val` **{Object}**
* `options` **{Object}**

**Example**

```js
questions.setDefault('author.name', 'What is your name?');
```

### [.get](index.js#L119)

Get question `name`, or group `name` if question is not found. You can also do a direct lookup using `quesions.cache['foo']`.

**Params**

* `name` **{String}**
* `returns` **{Object}**: Returns the question object.

**Example**

```js
var name = questions.get('name');
//=> question object
```

### [.del](index.js#L134)

Delete the answer for question `name` for the current (or given) locale.

**Params**

* `name` **{String}**: Question name
* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.del(locale);
```

### [.getAnswer](index.js#L171)

Get the answer for question `name` at the current cwd.

Optionally specify a locale to get, otherwise the default locale's
answer is returend.

**Params**

* `name` **{String}**
* `locale` **{String}**
* `returns` **{Object}**: Returns the question object.

**Example**

```js
var name = questions.getAnswer('name');
//=> {name: 'Jon'}

// specify a locale
var name = questions.getAnswer('name', 'fr');
//=> {name: 'Jean'}
```

### [.getDefaultAnswer](index.js#L197)

Get the default answer object for question `name` for the current locale. Optionally specify a locale to get the default answer for that locale.

**Params**

* `name` **{String}**
* `locale` **{String}**
* `returns` **{Object}**: Returns the question object.

**Example**

```js
var name = questions.getDefaultAnswer('name');
//=> {name: 'Jon'}

// specify a locale
var name = questions.getDefaultAnswer('name', 'fr');
//=> {name: 'Jean'}
```

### [.isAnswered](index.js#L216)

Return true if question `name` has been answered for the current locale and the current working directory.

**Params**

* `name` **{String}**: Question name
* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.isAnswered(locale);
```

### [.setData](index.js#L234)

Set data to be used for answering questions, or as default answers when `force` is true.

**Params**

* `key` **{String|Object}**: Property name to set, or object to extend onto `questions.data`
* `val` **{any}**: The value to assign to `key`

**Example**

```js
questions.setData('foo', 'bar');
// or
questions.setData({foo: 'bar'});
```

### [.hasData](index.js#L254)

Return true if property `key` has a value on `questions.data`.

**Params**

* `key` **{String}**: The property to lookup.
* `returns` **{Boolean}**

**Example**

```js
questions.hasData('abc');
```

### [.getData](index.js#L271)

Get the value of property `key` from `questions.data`.

**Params**

* `key` **{String}**: The property to get.
* `returns` **{any}**: Returns the value of property `key`

**Example**

```js
questions.setData('foo', 'bar');
questions.getData('foo');
//=> 'bar'
```

### [.question](index.js#L287)

Get the `question` instance stored for the given `name`. This is the entire `Question` object, with all answers for all locales and directories.

**Params**

* `name` **{String}**
* `returns` **{Object}**: Returns the question instance.

**Example**

```js
var name = questions.question('name');
```

### [.delQuestion](index.js#L328)

Delete a question.

**Params**

* `name` **{String}**: The question to delete.

**Example**

```js
question.deleteQuestion(name);
```

### [.getGroup](index.js#L396)

Get a group with the given `key`. If `key` has a dot, only the substring before the dot is used for the lookup.

**Params**

* `key` **{String}**
* `returns` **{Object}**

**Example**

```js
questions
  .set('author.name', 'Author name?')
  .set('author.url', 'Author url?')
  .set('project.name', 'Project name?')
  .set('project.url', 'Project url?')

var group = questions.getGroup('author');
//=> ['author.name', 'author.url']

questions.ask(group, function(err, answers) {
  // do stuff with answers
});
```

### [.deleteAll](index.js#L410)

Delete answers for all questions for the current (or given) locale.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.deleteAll(locale);
```

### [.erase](index.js#L428)

Erase all answers for question `name` from the file system.

**Params**

* `name` **{String}**: Question name

**Example**

```js
question.erase(name);
```

### [.ask](index.js#L473)

Ask one or more questions, with the given `options` and callback.

**Params**

* `queue` **{String|Array}**: Name or array of question names.
* `options` **{Object|Function}**: Question options or callback function
* `callback` **{Function}**: callback function

**Example**

```js
questions.ask(['name', 'description'], function(err, answers) {
  console.log(answers);
});
```

### [.getIndex](index.js#L576)

Get a the index of question `name` from the queue.

**Params**

* `name` **{String}**
* `returns` **{Object}**

**Example**

```js
questions.getIndex('author');
//=> 1
```

### [.unqueue](index.js#L621)

Remove a question from the queue.

**Params**

* `items` **{Object}**: Object of views

**Example**

```js
console.log(questions.queue);
//=> ['a', 'b', 'c'];
questions.unqueue('a');
```

### Question

### [Question](lib/question.js#L25)

Create new `Question` store `name`, with the given `options`.

**Params**

* `name` **{String}**: The question property name.
* `options` **{Object}**: Store options

**Example**

```js
var question = new Question(name, options);
```

### [.set](lib/question.js#L66)

Set the answer to the question for the current (or given) locale, at the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.set('foo');
```

### [.get](lib/question.js#L82)

Get the answer for the current (or given) locale for the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.get(locale);
```

### [.del](lib/question.js#L97)

Delete the answer for the current (or given) locale and the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.del(locale);
```

### [.erase](lib/question.js#L111)

Delete the answer store (all answers for the question) from the file system.

**Example**

```js
question.erase();
```

### [.isAnswered](lib/question.js#L127)

Return true if the question has been answered for the current locale and the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.isAnswered(locale);
```

### [.setDefault](lib/question.js#L142)

Set the default answer to use for the current (or given) locale, at the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.setDefault('foo');
```

### [.getDefault](lib/question.js#L157)

Get the default answer for the current (or given) locale

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.getDefault();
```

### [.hasDefault](lib/question.js#L173)

Return true if the question has been given a default answer for the current (or given) locale, at the current working directory.

**Params**

* `locale` **{String}**: Optionally pass a locale

**Example**

```js
question.hasDefault('foo');
```

### [.ask](lib/question.js#L194)

Ask the question.

* If an answer has already been stored for the current locale and cwd it will be returned directly without asking the question.
* If `options.force` is **true**, the answer will be asked asked even if the answer is already stored.
* If `options.save` is **false**, the answer will not be persisted to the file system, and the question will be re-asked each time `.ask()` is called (which means it's also not necessary to define `force` when `save` is false).

**Params**

* `options` **{Object|Function}**: Question options or callback function
* `callback` **{Function}**: callback function

**Example**

```js
question.ask({force: true}, function(err, answer) {
  console.log(answer);
});
```

### [.save](lib/question.js#L242)

Persist the answer for the current locale and cwd to disk.

**Example**

```js
question.save();
```

## Related projects

* [answer-store](https://www.npmjs.com/package/answer-store): Store answers to user prompts, based on locale and/or current working directory. | [homepage](https://github.com/jonschlinkert/answer-store)
* [inquirer](https://www.npmjs.com/package/inquirer): A collection of common interactive command line user interfaces. | [homepage](https://github.com/sboudrias/Inquirer.js#readme)
* [question-cache](https://www.npmjs.com/package/question-cache): A wrapper around inquirer that makes it easy to create and selectively reuse questions. | [homepage](https://github.com/jonschlinkert/question-cache)

## Running tests

Install dev dependencies:

```sh
$ npm i -d && npm test
```

## Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](https://github.com/jonschlinkert/question-store/issues/new).

## Author

**Jon Schlinkert**

* [github/jonschlinkert](https://github.com/jonschlinkert)
* [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

## License

Copyright © 2015 [Jon Schlinkert](https://github.com/jonschlinkert)
Released under the MIT license.

***

_This file was generated by [verb](https://github.com/verbose/verb) on December 13, 2015._