# test-npm-cli-pass

> Testing how NPM pass CLI options after -- on Windows

Passing arguments via NPM script `-- arg1 arg2` seems to be very broken if an argument
ends with backslash character `\`. This is common on Windows when passing paths, by default
shells add backslash when pressing Tab.

```
node v6.11.3
npm 5.4.2
```

[index.js](index.js) just prints the `process.argv`.

## correct

Correct behavior: options to pass via NPM script do not end with backslash character `\`

Can be seen by calling `npm t`

```
PS C:\Users\glebb\git\test-npm-cli-pass> npm t

> test-npm-cli-pass@1.0.0 test C:\Users\glebb\git\test-npm-cli-pass
> npm run demo -- --foo bar


> test-npm-cli-pass@1.0.0 demo C:\Users\glebb\git\test-npm-cli-pass
> node index.js "--foo" "bar"

process.argv
[ 'C:\\Program Files\\nodejs\\node.exe',
  'C:\\Users\\glebb\\git\\test-npm-cli-pass\\index.js',
  '--foo',
  'bar' ]
```

See last argument is just `bar` as we passed it.

## incorrect

But if the option passed via NPM script ends with backslash `\` then `-- foo\` will be incorrect - it
will acquire extra double quote `"` character.

Can be seen by calling `npm run test-win`

```
PS C:\Users\glebb\git\test-npm-cli-pass> npm run test-win

> test-npm-cli-pass@1.0.0 test-win C:\Users\glebb\git\test-npm-cli-pass
> npm run demo -- --foo c:\bar\


> test-npm-cli-pass@1.0.0 demo C:\Users\glebb\git\test-npm-cli-pass
> node index.js "--foo" "c:\bar\"

process.argv
[ 'C:\\Program Files\\nodejs\\node.exe',
  'C:\\Users\\glebb\\git\\test-npm-cli-pass\\index.js',
  '--foo',
  'c:\\bar"' ]
PS C:\Users\glebb\git\test-npm-cli-pass>
```

See `'c:\\bar"'`?

## very incorrect

And if you pass two arguments ending with backslash, forget it! `npm run test-win2`

```
PS C:\Users\glebb\git\test-npm-cli-pass> npm run test-win2

> test-npm-cli-pass@1.0.0 test-win2 C:\Users\glebb\git\test-npm-cli-pass
> npm run demo -- c:\bar\ c:\baz\


> test-npm-cli-pass@1.0.0 demo C:\Users\glebb\git\test-npm-cli-pass
> node index.js "c:\bar\" "c:\baz\"

process.argv
[ 'C:\\Program Files\\nodejs\\node.exe',
  'C:\\Users\\glebb\\git\\test-npm-cli-pass\\index.js',
  'c:\\bar" c:\\baz"' ]
```

Instead of two arguments we get single argument with each original one ending with extra `"` character.

