#Parser

`Parser` is a constructor. Instantiate a new `Parser` with

```javascript
var parser = new lacona.Parser(options);
```

##constructor `lacona.Parser(options)`

`options` can contain the following properties, which can be set after construction as well:

- `sentences: [String]` - an array of names of phrases that should be treated as complete parsable entities. For example, if you want to use Lacona to accept a date, but the grammar that you `understand` also includes `day-of-the-week`, you should specify `sentences` to be `['date']`.
- `langs: [String]` - an array of acceptable input languages, in the order that the parser should prefer. Each language should be specified in IETF format (e.g. `es` for Spanish, `en-US` for English spoken in the US). For example, if I speak Castilian Spanish as my first language, but also learned English in the UK, my `langs` should probably be `['es-ES', 'es', 'en-GB', 'en']`. If a phrase does not support any of those languages, `default` will always be used.
- `fuzzy: Boolean` - if true, Lacona will use fuzzy parsing similar to Sublime Text. That is, input can skip letters and Lacona will do its best to understand.

##method `parser.understand(grammar)`

See [grammar](doc/grammar.md) docs.

Returns the `Parser` instance for chaining.

##method `parser.use(middleware)`

Takes a middleware function that will be used to modulate the data coming from Lacona. All middleware will be executed in the order that it was `use`d. The most common use for this is to take the data and transform it into something that can be displayed. While this may not be useful on the server or in a framework like AngularJS, it could come in handy for simple sites or tools that want to make use of Lacona's functionality.

`middleware` must be a function that takes 2 arguments, `data` which represents the actual data coming from Lacona (or previous middleware), and a callback `done`. The function is expected to modify `data` in some way and call `done` with an `Error` or nothing. If an `Error` is supplied at any point, middleware execution will stop and the parser will emit an `error` event. Otherwise, calling `done` will pass `data` to the next middleware if one exists, or th parser will emit the `data`.

Middleware will only be called for `data` events

Returns the `Parser` instance for chaining.

##method `on(event, handler)`

This allows you to set handlers that respond to the parser's events. Just like node.js streams, `Parser` will trigger 3 events: `data`, `end`, and `error`. To handle one of these events, just pass the name of the event as the first argument, and a `Function` to handle it as the second.

Returns the `Parser` instance for chaining.

###`data` event

This event is triggered as soon as the `Parser` gets a successful parsing structure. It is passed a single argument, `option`, which is a representation of the parse (after being passed through all middleware). These may be called in any order. If order matters, ... #Figure this out#

###`end` event

Called with no arguments when all possible options have been delivered. `data` will never be called after `end` is, for a single parse.

###`error` event

Something went wrong. `end` will not be called.