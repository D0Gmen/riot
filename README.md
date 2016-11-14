
## The story

I've been a long time user of Riot and find it the simples way to build user interfaces. When you write a custom HTML component such as this:

```
<hello>
  <ul>
    <li each={ item, i in items }>
      { item }
    </li>
  </ul>

  <script>
    // logic here
    this.items = []
  </script>

</hello>
```

It just feels obvious. You're right at home with the layout and logic.

And indeed, it seems the web has settled to components. The tight coupling of HTML and JS is a best practise, almost like a standard. All the succesfull frameworks today rely on this foundation.

I hope the above kind of syntax will become the de facto way to write components, because the current HTML component spec is too complex and has failed to gain adoption. Just like the standard DOM interface "lost" the battle for jQuery.

Given the importance of this project and the roadmap of 3.0 I felt the pressure to take a holistic look of the Riot source. It turned out that some of the concerns are so drastic that the current architecture cannot hold everything together without significant amount of hacks or glue code.

Another thing that caught my eye is the file size of Riot, which seems to grow steadily. It hurts the minimalistic Riot brand as well as makes the project less tempting for contributors. Especially the `each.js` is a growing concern because of the inner complexity.

This is a rewrite of Riot. An attempt to make a solid architecture that can be easily extended without hacks. It solves big issues while keeping things small.

Here's the summary of changes and new features.


## Major improvements

Following improvements were not possible without a bigger architectural change.

1. No need for Content Security Policy rules. The compiler turns expressions into real JavaScript functions. No more complaints about eval being evil.

2. True `if`- expressions, mount and unmount. When `if` returns `false` nothing gets excecuted on the custom tags within the clause.

3. Standardized parent- and child relationship. No complex `prototype` chains or `parent`- references on unobvious places.

4. Clean generated HTML without redundant root elements. The DOM layout is exactly what is defined on the tags. All internal, unstandard attributes are stripped out.

5. Performant loops. A call such as `items.push(elem)` will update the array as well as the corresponding DOM collection in place. All array operations: `unshift`, `splice`, `sort`... are executed as fast as it can get.

6. Riot "Next" is 3.5kb minified and 1.4kb gzipped ATM.



## New features
I felt following features were lacking:

1. Unescaped HTML with double quotes: `<div>{{ unescaped }}</div>`.

2. Supplying arguments for event handlers: `onclick={ setState('active') }`

3. Auto-assign of commonly used variables: `self`, `refs` and `tags`. These are real variables and won't clutter the instance namespace.

4. Meaningful error messages on the expressions

5. Meaning compiler error messages such as "Unbalanced tag". The compiler now traverses the HTML with `simple-dom` having deeper understanding of the layout.


## Breaking changes
Due to following changes (on the tag syntax) a new major version number is required.

1. All logic must now be placed inside a `<script>` node.

2. The default extension for tag files is `htm` or `html` because HTML is the actual syntax of writing tags.

3. No IE8 support == hack free code.

4. No support for server side rendering. I haven't seen a succesfull isomorphic application so far. We can make this if there is a real need.

5. `preventDefault` is not called by default


## Questions

1. Do we need `observable`? Can it be replaced with custom DOM events and `onmount`/`onunmount` instance methods.

2. What pre-processors are needed: TypeScript, ES6 ...?

3. Do we need the loop index variable? ie. the `i` on `{ item, i on items }`. It's doable but complex since it must be re-calculated on every change in the array.


## Roadmap
Not yet done, but required for a release.

1. Simple and solid nested loops as well as handling of complex combinations of `if` and `each`. These make the core architecture that everything else is tied to.

2. Nested `<style>` tags.

3. The `<yield>` tag.

4. Object loops.

5. Compiler support for mixed JS and (multiple) tags

6. A test suite. I'd like to see a lot of small unit tests since the codebase is split into many small functions. Separate suites for node (quick) and phantom.

7. Split into projects: compiler, cli, demo and riot.


