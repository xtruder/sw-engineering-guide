# NodeJs

> NodeJS is the only real dev language

## Programmer notes

We still see many developers writing ancient es5 javascript code. In a world where
we have node 7, that supports almost all es6 and even some features of es7, it's
insane to write in code style of internet explorer 8. Even for ancient interpreters,
there are compilers that can transpile code to whatever ancient javascript you need.
While it's understandable that you do should not need to use all bleeding edge features you
could at least stop writing an acient code and at least use javascript that somehow
looks like a real language.

These are basic essentials that you should use and should avoid in node.js:

### Install new node.js (it's back compatible):

While your distro probably provides some old stable node.js, you are a developer
that does not live a few years back. While i know developers are generally lazy,
and so are sysadmins, please install and use a decent version of node.js.

https://nodejs.org/en/download/package-manager/

### Stop using callback pattern

Promises exists in javascript from ancient nodejs 0.12. That's even before node.js
had real versioning and looked like unstable beta. While i see still many libraries
that use callback pattern, these are old libraries, that you should avoid at all cost
if you are any real node.js programmer.

If you have to deal with some ancient library, you can still wrap code with a promise:

```
new Promise((resolve, reject) => {
  my_ancient_function(err => err ? reject(err) : resolve());
});
```

### Avoid lodash/underscore when possible

While i have been a big fan of bluebird like libraries which provided framework for working
with javascript structures, it turns out that es6 and es7 get much better internal support
for working with js structures:

- list.filter
- list.find
- list.forEach
- list.map
- list.recude
- list.includes
- Object.assign
...

If you using these internal javascript methods, they are much faster, and provide nicer stack
traces which allows easier debugging.

#### Avoid promise libraries when possible

NodeJS has internal support for promises. Using internal promises, will not give you such rich
functionality, but will provide much better debugging support and nicer stack traces.

#### Avoid compiling javascript if not needed

I know there are fancy bleeding es7 or es8 features that you want to abuse, or you really want
to use that typescript, that you have no idea how to use right, as you will soon be just writing
normal javascript. Please don't, use native node.js 7 features instead. While your code won't be
totaly bleeding edge, you will at least have some sane ways to debug. And i know you are master
`console.log` debugging guru, your sysadmin is not. When you will beg him to debug this mistery
bug in production, the only thing he will have on his dispose is a debugger. He will be much happier
if he would actually be able to read code.

If you need to compile, use at least decent compiler, like typescript. Babel produces search and
replace garbage.

### Use async/await

Async/await is a new promise syntastic sugar that will get rid of ugly promises and replace with
something that async language like javascript should have from day one. It's supported in node.js 7
using `--harmony-async-await` flag and you should defenitelly be using it. Even if your production does
not have node.js 7 or you don't want to use beta features, you can use babel to compile.

Unuglify example:

```
// orders all cheese pizza
Restaurant.get(id).then(restaurant => {
  return Menu.query(restaurant.id).then(items => {
    return Promise.all(items.map(item => {
      if (item.kind === 'pizza') {
        return Ingiridients.getForItem(item.id).then(ingiridents => {
          for (const ingridient of ingiridents) {
            if (ingridient.name === 'cheese') {
              return Restaurant.order(item);
            }
          }
        });
      }
    }));
  });
});
```

To this: 

```
const restaurant = await Restaurant.get(id);
const items = await Menu.query(restaurant.id);
const pizzas = items.filter(item => item.kind === 'pizza');

await Promise.all(pizzas.map(async pizza => {
   const ingridients = await Ingiridients.getForItem(pizza.id);
   
   if (ingridents.find(ingridient => ingiridient.name === 'cheese')) {
     await Restaurant.order(pizza);
   }
}));
```

Much nicer!