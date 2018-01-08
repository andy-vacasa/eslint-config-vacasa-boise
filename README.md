# eslint-config-vacasa

JavaScript and JXS code linting with Vacasa (Boise) standard styles.

Industry standard for this sort of thing is to start with [Airbnb's styles](https://github.com/airbnb/javascript), and adds rule overrides for things we do differently.  They tend to be pretty heavy handed and we are also interested in consistency between JavaScript and Python, so there are a few things we will do differently.

## Installing and Using

Git clone the repo, then `npm install` the required eslint packages.  You could probably install all those things globally instead and you'd just need the .eslintrc file here.  It is important that everything lives at the same scope, either all global or all local.

IntelliJ does a good job of automatically running inspections.  Open the plugin at Languages & Frameworks, JavaScript, Code Quality Tools, ESLint.  If its not there, install the plugin.  Then set these fields:
* Click the Enable toggle.
* Set the node interpreter.  Shouldn't really matter which one you use, there just needs to be one.
* ESLint package will be the directory where eslint is installed.  Either locally at node_modules/eslint, or wherever it lives globally.
* Instead of automatic configuration, manually select Configuration File and set it to the .eslintrc file in this project.  Note that hidden files on MacOS can be seen in the file browser by pressing Cmd+Shift+Period.

There are a few automatic fixing commands in IntelliJ, but be aware that they don't always follow each other's rules.  A good way to properly format a file is to first perform a Reformat Code with Cmd+Option+L, then alt-click in the code and select "Fix ESLint Problems" in the context menu.  This should get you pretty close.

## Looking up error messages

Mousing over eslint errors in IntelliJ will report an error message and the name of the rule that was violated.  You can look up these errors on the project pages for each of the several plugins being run.  These pages offer good documentation for why the rule exists and usually examples of the right way to do it.

Check these pages, depending on the prefix of the error rule
* (none) https://github.com/eslint/eslint/tree/master/docs/rules
* (import/) https://github.com/benmosher/eslint-plugin-import/tree/master/docs/rules
* (react/) https://github.com/yannickcr/eslint-plugin-react/tree/master/docs/rules
* (a11y/) https://github.com/evcohen/eslint-plugin-jsx-a11y/tree/master/docs/rules
* (babel/) https://github.com/babel/eslint-plugin-babel/tree/master/rules

## Overridden Rules

Some documentation for why we have overridden certain rules.

#### indent, react/jxs-indent, jsx-indent-props

Default indentation is 2, but we use 4.  We think its better to be consistent with our backend Python which uses 4 spaces.  We also shoudn't need as much indentation in our JS since we are using React and ES6, which allows better structures than the usual big triangles of callbacks.

#### spaced-comment

Requires a space after comment start, which is awfully picky.

#### quotes

Prefer single quotes for strings.  There are cases in JS-like languages where this matters, so lets not start down the path of preferring one.

#### jsx-quotes

Same rule as "quotes" but within jsx blocks.

#### comma-dangle

This rule prefers the last element in an array or object to have a comma as though it weren't the last in order to make commit diffs cleaner.  JSON *cannot* have trialing commas, and I hate switching between these styles.

#### no-mixed-operators

This rule wants you to be extra-specific about order of operations by not allowing different operations without parenthesis.  We know the rules.

#### prefer-destructuring

There are some syntactic sugar ways of accessing array and object elements in es6, which I don't think we should require using.

#### prefer-template

This prefers templated strings over concatentation.  Use whatever is cleanest for each use case.

#### camelcase

JS standard is camelCase, while Python in underscore_separated.  We tend toward consistency across languages.

#### object-shorthand

ES6 allows object shorthand when a key matches a variable to assign to it.  For example, the statements `{value: value}` and `{value}` are equivalent.  This rule enforces the latter, but there are many cases where this would make things less consistent, eg: `{value, foo:bar}`.

#### no-restricted-syntax

The list here is standard except we have removed the restrictions against ForOfStatement.  This is a straightforward way to iterate over an array, lets use it.

#### react/prefer-stateless-function

There are a few ways to write custom React components.  We have been using es6 classes which are clean and easy to understand.  Writing them as a function will theoretically gain some performance bonus from some future version of React.  Thats a lot of maybes to sarifice readability.

#### react/jsx-wrap-multilines

This prefers jsx tags that span multiple lines to also be enclosed in parenthesis.  This seems reduntant as the tags themselves specify the jsx context, and it is inconsistent to add additional characters if you go from single to mulitple lines.

#### react/jsx-closing-bracket-location

We have changed the setting to "line-aligned", which requires that the closing bracket be in the same column as the start of the line where the tag started, instead of the same column as the opening tag.  This keeps our indentation cleaner.

#### react/jsx-closing-tag-location

This setting wants multiline closing tags to be on the same column as the opening tag, but this looks really strange if the opening tag isn't on the beginning of the line.  eg: return <div ...

A similar setting react/jsx-closing-bracket-location allows the setting value "line-aligned", which prefers the closing tag to have the same indentation as the *line* of the opening tag instead of the *column* of the tag, and there is an [open issue](https://github.com/yannickcr/eslint-plugin-react/issues/1466) to implement this same setting for jsx-closing-tag-location.  Once implemented, we should re-enable this rule but with the "line-aligned" setting value.

#### babel/semi

This is set to "error" for static class properties because the spec requires them.  Although babel is able to understand these and insert a semicolon when translating, it would be best to stick to the spec and write these statements like any other.


## Adding new rules

If we discover new rules that we would rather deviate from, then can be added as new rules to the .eslintrc file.  Rules that come from plugins will be prepended with that plugin name in both the error message and the rule to add.  See the file for examples of how to add new rules.
