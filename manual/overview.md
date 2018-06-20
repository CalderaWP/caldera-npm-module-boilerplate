# Caldera Whatever API

## Documentation Of the Example Module
This is a guide for documenting how to use your actual module.

### `sayHi`

```js
//Say Hi to Roy
console.log(sayHi()); //Hi Roy
```


```js
//Say Hi to Someone else
console.log(sayHi('Mike')); //Hi Mike
```


## Using This Boilerplate

### Enable Github Pages For Docs

Go to Settings in Github repo.

Enable Github pages for master branch /docs dir
<a href="https://ibb.co/bTSUv8"><img src="https://preview.ibb.co/hsXwa8/Screen_Shot_2018_06_09_at_3_41_30_PM.png" alt="Screen_Shot_2018_06_09_at_3_41_30_PM" border="0"></a>




### React Component Development
#### Setup npm for React dev
Add React, React DOM and some form of [react scripts](https://www.npmjs.com/package/react-scripts) as dependencies in package.json. In this example, [humanmade/react-wp-scripts](https://github.com/humanmade/react-wp-scripts) is used.
```
{
"dependencies": {
    "react": "^16.4.0",
    "react-dom": "^16.4.0",
    "react-wp-scripts": "0.2.0"
  }
}
```

Then add a start command to scripts and use react scripts for tests and test:once in package.json

```json
{
 "scripts" : {
    "start": "react-wp-scripts start",
        "test": "react-scripts test --env=jsdom",
        "test:once": "CI=true react-scripts test --env=jsdom",
 }
}

```

Notes:
* Test once command once here: https://github.com/facebook/create-react-app/issues/1137#issuecomment-265292520
* If you get an error `SyntaxError: Unexpected token <` from the logo.svg, you probably forgot to change the tests command.

In README.md, document this new script:

```markdown
### Develop
* `yarn start`
    - Runs linter and compiles for development
    - Starts test app server
```

#### Add Main App Component
Copy this from [calderawp/react-start](https://github.com/calderawp/react-start) or build your own.

#### Adding State Management
##### State Management Development Guidelines
* We use [Redux](https://redux.js.org/)-like state management.
* Try and use 1-2 [container components](https://redux.js.org/basics/usage-with-react#implementing-container-components) and pass state down to stateless, [controlled components](https://reactjs.org/docs/forms.html#controlled-components).
* A component for UI, should not own its state management. That's a separate concern, it goes elsewhere.
* Use [@caldera-labs/state package](https://github.com/calderawp/caldera-state). If that module does not have the features needed, either add them or make a new module extending it.
* Try to use [@wordpress/data](https://www.npmjs.com/package/@wordpress/data) over Redux when possible.

##### Helpful Resources About State Management In React This Way
* https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
* https://css-tricks.com/learning-react-container-components/
* https://www.youtube.com/watch?v=v6iR3Zk4oDY

#### Creating Components
Components must have one responsibility. They can contain components with additional responsibilities.

##### React Component Guidelines
* [controlled components](https://reactjs.org/docs/forms.html#controlled-components)
* [Functional, not class components](https://reactjs.org/docs/components-and-props.html#functional-and-class-components). Redux owns state, we do not need to manage its update cycle. Functional components are highly testable and more likely to play nicely with [async rendering](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)

##### React Component Example
```js
import React from 'react';
import PropTypes from 'prop-types';

/**
 * Basic input component
 * 
 * @param {Object} props
 * @returns {*}
 * @constructor
 */
export const Input = (props) => {
	function changeHandler(event){
		return props.onValueChange(event.target.value);
	}
	return (
		<input
			type={props.inputType}
			id={props.id}
			className={props.fieldClassName}
			aria-describedby={props.ariaDescribedbyAttr}
			required={props.isRequired}
			onChange={changeHandler}
			value={props.value}
		/>
	);

};

/**
 * Prop definitions for Input components
 */
Input.propTypes = {
	id: PropTypes.string.isRequired,
	fieldClassName: PropTypes.string.isRequired,
	help: PropTypes.string,
	value: PropTypes.any(
		PropTypes.string,
		PropTypes.number,
		PropTypes.array
	),
	onValueChange: PropTypes.func.isRequired,
	inputType: PropTypes.string.isRequired,
	options: PropTypes.array,
	ariaDescribedbyAttr: PropTypes.string,
};

/**
 * Default props for Input Component
 * 
 * @type {{ariaDescribedbyAttr: string, isRequired: boolean, inputType: string}}
 */
Input.defaultProps = {
	ariaDescribedbyAttr: '',
	isRequired: false,
	inputType: 'text'
};
```

##### Good Reads About Component Development In React This Way
* https://codeburst.io/when-to-use-component-or-purecomponent-a60cfad01a81
* https://60devs.com/pure-component-in-react.html
* https://medium.com/@antonkorzunov/2-things-about-purecomponent-you-probable-should-know-b04844a90d4
### Creating Exports
This boilerplate compiles code with babel to the `/dist` directory. One or more module exports must be created and identified in package.json.

For example:
```json
{
"main": "./dist/index.js",
  "files" : [
      "./dist/index.js",
      "./dist/fields/index.js",
      "./dist/fields/factories/index.js"
  ]
}
```

See:
* https://docs.npmjs.com/files/package.json#files
* https://docs.npmjs.com/files/package.json#main

### Publishing To NPM
All Caldera libraries should be published as [org scoped packages](https://www.npmjs.com/docs/orgs/publishing-an-org-scoped-package.html) in the [@caldera-labs](https://www.npmjs.com/search?q=%40caldera-labs) organization.

Short version of [the docs](https://www.npmjs.com/docs/orgs/publishing-an-org-scoped-package.html)

Make sure in package.json:

* That `private` is not set to true.
* That the name of the package is `@caldera-labs/whatever` where `whatever` describes the modules job. For example, `@caldera-labs/mailchimp-client` if you're building a Mailchimp client. Note that "caldera" was not used on the right side of the slash.
* That you have specified at least one entry point and defined a module export for it.
* You have authorization to publish in `@caldera-labs` org scope.
* You are logged in as that user via the npm cli
    - https://docs.npmjs.com/cli/adduser

Once you're ready to publish, commit everything and do the first release manually:

`npm publish --access public`

After that, the documented scripts for automated update publishing apply for future updates.