# chevrotain-pegjs

This is a PEGjs grammar parser class with a built-in Lexer. The purpose of this module is to allow language developers to write LL(K) compatible grammars in PEGjs syntax and automatically produce a parser for that grammar. Chevrotain is used as the backing library for both the PEGjs grammar parser as well as the parser for the target language. So familiarity with Chevrotain is a must.

## API
The PEGParser class is the object received from requiring this module

```js
let PEGParser = require("chevrotain-pegjs");
```

To use it, just create a new instance, passing your new language's PEGjs text as the parameter.

```js
let pegParser = new PEGParser(YOUR_PEGjs_TEXT);
```

Although it's not always necessary (since chevrotain-pegjs will extract and name the tokens for you), it's usually better to provide your own token ordering. To do so, first create an array of objects. Each object should have a single key with the token name as the key and the regular expression to match that token as the value.

```js
let lexMap = [
    { Whitespace: /[\s\t\r\n]/ },
    { If: /if/ },
    { When: /when/ },
    ...
];
```

Use this array as the 2nd parameter when instantiating the PEGParser.
```js
let pegParser = new PEGParser(YOUR_PEGjs_TEXT, lexMap);
```

That performs the lexing for your language using Chevrotain. Follow this by calling `learnLanguage()` with the name of your language as the parameter.

```js
let YourLangParser = pegParser.learnLanguage("YOUR_LANG_NAME");
```

If you specified your own token ordering, you should also provide that array to `learnLanguage()` as the 2nd parameter.
```js
let YourLangParser = pegParser.learnLanguage("YOUR_LANG_NAME", lexMap);
```

Now just instantiate that `YourLangParser` and you have the parser for your new language.

#### Important Note
To generate the parser object, Chevrotain uses `eval()`. If the Content Security Policy of your target environment does not allow the use of `eval`, you must use source code generation to get your parser's source and load that by whatever means allowed in your target environment.

For more details, see [Chevrotain: Code Generation](https://sap.github.io/chevrotain/docs/guide/custom_apis.html#code-generation).

## Using The New Parser
Since Chevrotain's `Lexer` requires you pass in your language's tokens, you can just fetch them from your new parser instance.

```js
let parser = new YourLangParser(chevrotain_options);
let lexer = new Lexer(Object.values(parser.tokensMap));
```

From here on out, just do what you would with any chevrotain lexer and parser.

```js
let lexResult = lexer.tokenize(YOUR_LANG_SOURCE_CODE);
if (lexResult.errors.length > 0) {
    throw new Error("Oh no! You've got syntax errors!");
}

parser.input = lexResult.tokens;
let cst = parser.Expression();
```

If you want to see this in action using a simple sample grammar, just look at the [example code](./test/index.js) in the test directory.

## Source Code Generation
If you would rather generate the source code for the parser instead of the parser object itself, set the 3rd parameter of `learnLanguage()` to `true`.

```js
let YourLangParser = pegParser.learnLanguage("YOUR_LANG_NAME", null, true);
```
or
```js
let YourLangParser = pegParser.learnLanguage("YOUR_LANG_NAME", lexMap, true);
```



## Installation
It's an ordinary node module that requires chevrotain, so...

```
npm i chevrotain --save
npm i chevrotain-pegjs --save
```

## References
* [Chevrotain](https://sap.github.io/chevrotain/docs/) - the core grammar parsing API
* [PEGjs](https://pegjs.org/documentation) - the parser generator whose grammar is targeted
* [Example Code](./test/index.js) - example logic used to test this module


### Have fun writing those new languages!
