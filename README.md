# Frontend Testing

- **The past grads have used ‘@testing-library’ to test their front end.**
    
[https://github.com/sky-uk/id-nemesis/blob/main/groot/](https://github.com/sky-uk/id-nemesis/blob/main/groot/)
    
- **External Links to sources used in this notion**
    
[https://testing-library.com/docs/react-testing-library/intro/](https://testing-library.com/docs/react-testing-library/intro/) is the docs for the testing library used.
    
[https://blog.logrocket.com/testing-react-components-react-testing-library-vs-enzyme/](https://blog.logrocket.com/testing-react-components-react-testing-library-vs-enzyme/).
    
[https://fek.io/blog/add-jest-testing-framework-to-an-existing-next-js-app/](https://fek.io/blog/add-jest-testing-framework-to-an-existing-next-js-app/)
    

## Jest

Jest is a popular testing framework that is often used with React applications. It supports snapshot testing, assertion methods like expect, and mocking. It's known for its simplicity and ease of use.

![Testing](https://blog.logrocket.com/wp-content/uploads/2022/03/state-javascript-survey-2022.png)

[https://2022.stateofjs.com/en-US/libraries/testing/]

## React Testing Library

React Testing Library: This library focuses on testing the behaviour of your components from a user's perspective. It encourages testing your components the way a user would interact with them. It provides querying methods like getBy, findBy, and queryBy to find elements in your rendered component and make assertions on them.

### Button Testing

```jsx
// components/Button.js
import React from 'react';

const Button = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);

export default Button;
```

```jsx
// __tests__/Button.test.js
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import Button from '../components/Button';

test('calls onClick prop when button is clicked', () => {
  const handleClick = jest.fn();
  const { getByText } = render(<Button label="Click me" onClick={handleClick} />);
  
  fireEvent.click(getByText('Click me'));
  
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

### Navigation Link Testing

```jsx
// components/NavLink.js
import React from 'react';
import Link from 'next/link';

const NavLink = ({ to, children }) => (
  <Link href={to}>
    <a>{children}</a>
  </Link>
);

export default NavLink;
```

```jsx
// __tests__/NavLink.test.js
import React from 'react';
import { render } from '@testing-library/react';
import NavLink from '../components/NavLink';

test('renders the navigation link', () => {
  const { getByText } = render(<NavLink to="/about">About</NavLink>);
  const linkElement = getByText('About');
  
  expect(linkElement).toBeInTheDocument();
  expect(linkElement.getAttribute('href')).toBe('/about');
});
```

## Enzyme

When setting up enzyme, follow the official docs for it. Will include how to set up the enzyme adapter.

[https://enzymejs.github.io/enzyme/](https://enzymejs.github.io/enzyme/) 

Enzyme is a JavaScript Testing utility for React that makes it easier to test your React Components' output. You can also manipulate, traverse, and in some ways simulate runtime given the output.

Enzyme's API is meant to be intuitive and flexible by mimicking jQuery's API for DOM manipulation and traversal.

[https://blog.logrocket.com/getting-started-with-enzyme-for-react-a106b58fc53b/](https://blog.logrocket.com/getting-started-with-enzyme-for-react-a106b58fc53b/) is a good link to getting started with Enzyme.

On the Enzyme official page, we can read:

“Enzyme is a JavaScript Testing utility for React that makes it easier to test your React Components' output. You can also manipulate, traverse, and in some ways simulate runtime given the output.”

The library allows us to use 3 different ways of rendering React components in our tests:

- [Shallow Rendering](https://enzymejs.github.io/enzyme/docs/api/shallow.html)
- [Full DOM Rendering](https://enzymejs.github.io/enzyme/docs/api/mount.html)
- [Static Rendering](https://enzymejs.github.io/enzyme/docs/api/render.html)

Enzyme is dependent on React internals, so apart from the library itself, we need to install an Adapter that is compatible with the version of React in the project.

React Testing Library provides only one way of rendering React components which is "render" - equivalent to Enzyme's "mount".

When using React Testing Library, we are working on rendered DOM nodes, so we can write tests that behave as the user would. React Testing Library is testing the UI from the "outside", so when a new version of React is released, there is no need to make any changes in the library source code.

### Enzyme v RTL

The big difference between these two libraries is the approach to testing React components.

In Enzyme, we have access to the props, state, and children of rendered React components. We can read and set the state to test the component behaviour, depending on the inputs, and as a result, the tests are *written from the developer's perspective*.

**React Testing Library focuses more on testing the components in a way the user would use them**. We can query elements from the DOM by searching for texts, labels, etc. With this approach, we don’t access the internals of the components, but instead, we check if the output of the component and its behaviour are correct. 

This might give us more confidence in our tests because we are always testing if the component works correctly from the *user's point of view.*

Enzyme is less supported than RTL.

**Enzyme works best with class-based components**. It has a lot of functionalities that make it easy to test class components and their typical functions, such as componentDidMount(), componentWillUnmount(), etc. In the official [React documentation](https://reactjs.org/docs/test-utils.html#overview), we can read that Enzyme makes it easier to operate on the React Components output, version 16 and older, where class components were still popular.

React Testing Library will perform better in functional-based components. The official React documentation recommends using it whenever possible.

## Enzyme example testing

[https://blog.logrocket.com/getting-started-with-enzyme-for-react-a106b58fc53b/](https://blog.logrocket.com/getting-started-with-enzyme-for-react-a106b58fc53b/)

```bash
import React from 'react';

class Login extends React.Component {
    constructor() {
        super()
        this.state = {
            username: '',
            password: ''
        }
    }

    handleInputChange = (event) => {
        this.setState({
            [event.target.name]: event.target.value
        })
    }
    
    render() {
        return (
            <form className='login'>
                <label>Username</label>
                <input id='email' onBlur={this.handleInputChange} name='email' type='text' />
                <label>Password</label>
                <input id='password' onBlur={this.handleInputChange} name='password' type='password' />
                <button>Submit</button>
            </form>
        )
    }
}
export default Login
```

### Testing the above component

```bash
import React from 'react';
import { shallow, mount, render } from '../../enzyme';
import Login from '../Login'

describe('Login Test Suite', () => {

    it('should render the form', () => {
        const wrapper = shallow(<Login />);

        expect(wrapper.find('form.login').exists()).toBe(true);
        expect(wrapper.find('#email').length).toEqual(1);
        expect(wrapper.find('#password').length).toEqual(1);
    })
})

describe('Email Test Suite', () => {

    it('should change the state of the Login component', () => {

        const wrapper = shallow(<Login />);
        wrapper.find('#email').simulate('blur',
            {
                target: { name: 'email', value: 'logrocket@mail.com' }
            });

        expect(wrapper.state('email')).toEqual('logrocket@mail.com');
    })
})

describe('Password Test Suite', () => {

    it('should change the state of the Login component', () => {

        const wrapper = mount(<Login />);
        wrapper.find('#password').simulate('blur',
            {
                target: { name: 'password', value: 'my log is rocket' }
            });

        expect(wrapper.state('password')).toEqual('my log is rocket');
    })
})

```

The first test suite is nothing new; we’re just checking if the form elements exist. The second and third tests are making use of the `simulate()` function to, as the name suggests, simulate an event in the field — in this case, `onBlur`.

Once we’ve set that the `onBlur` will trigger the state update of each input field, we can then check if the same state was stored. That’s a great example of a behaviour test, wherein we’re testing what happens after Enzyme forces the simulation of an event in the component.

## Testing functional components with Enzyme

```bash
import React from 'react';

export default function Login(props) {
  const { email: propsEmail, password: propsPassword, dispatch } = props;
  const [isLoginDisabled, setIsLoginDisabled] = React.useState(true);
  const [email, setEmail] = React.useState(propsEmail || '');
  const [password, setPassword] = React.useState(propsPassword || '');

  React.useEffect(() => {
    validateForm();
  }, [email, password]);

  const validateEmail = text => /@/.test(text);

  const validateForm = () => {
    setIsLoginDisabled(password.length < 8 || !validateEmail(email));
  };

  const handleEmailBlur = evt => {
    const emailValue = evt.target.value.trim();
    setEmail(emailValue);
  };

  const handlePasswordChange = evt => {
    const passwordValue = evt.target.value.trim();
    setPassword(passwordValue);
  };

  const handleSubmit = () => {
    dispatch('submit(email, password)');
    setIsLoginDisabled(true);
  };

  return (
    <form>
      <input
        type="email"
        placeholder="email"
        className="mx-auto my-2"
        onBlur={handleEmailBlur}
      />
      <input
        type="password"
        className="my-2"
        onChange={handlePasswordChange}
        value={password}
      />
      <input
        type="button"
        className="btn btn-primary"
        onClick={handleSubmit}
        disabled={isLoginDisabled}
        value="Submit"
      />
    </form>
  );
}
```

The above is a functional component in React with hooks that we can test.

```bash
import React from 'react';
import { shallow } from 'enzyme';
import Login from '../index';

describe('<Login /> with no props', () => {
  const container = shallow(<Login />);
  it('should match the snapshot', () => {
    expect(container.html()).toMatchSnapshot();
  });

  it('should have an email field', () => {
    expect(container.find('input[type="email"]').length).toEqual(1);
  });

  it('should have proper props for email field', () => {
    expect(container.find('input[type="email"]').props()).toEqual({
      className: 'mx-auto my-2',
      onBlur: expect.any(Function),
      placeholder: 'email',
      type: 'email',
    });
  });

  it('should have a password field', () => { /* Similar to above */ });
  it('should have proper props for password field', () => { /* Trimmed for less lines to read */ });
  it('should have a submit button', () => { /* */ });
  it('should have proper props for submit button', () => { /* */ });
});
```

Now to test the *login components with props passed* we copy the same method as above and update the necessary props that would change when the ***initialProps*** are passed.

```bash
describe('<Login /> with other props', () => {
  const initialProps = {
    email: 'acesmndr@gmail.com',
    password: 'notapassword',
  };
  const container = shallow(<Login {...initialProps} />);

  it('should have proper props for email field', () => {
    expect(container.find('input[type="email"]').props()).toEqual({
      className: 'mx-auto my-2',
      onBlur: expect.any(Function),
      placeholder: 'email',
      type: 'email',
    });
  });

  it('should have proper props for password field', () => {
    expect(container.find('input[type="password"]').props()).toEqual({
      className: 'my-2',
      onChange: expect.any(Function),
      type: 'password',
      value: 'notapassword',
    });
  });

  it('should have proper props for submit button', () => { /* */ });
});
```

[https://acesmndr.medium.com/testing-react-functional-components-with-hooks-using-enzyme-f732124d320a](https://acesmndr.medium.com/testing-react-functional-components-with-hooks-using-enzyme-f732124d320a) is the link followed for enzyme testing.

# How to Setup

## Install Enzyme

Follow the docs for better guide: [https://enzymejs.github.io/enzyme/docs/installation/](https://enzymejs.github.io/enzyme/docs/installation/)

## ****Install Jest and React Testing Library****

```bash
npm install jest @testing-library/react @testing-library/jest-dom jest-environment-jsdom --dev
```

### Add Jest Config

The next thing we need to do is add a `jest.config.js` file to the root directory of our project, and code the following configuration:

```jsx
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  // Provide the path to your Next.js app to load next.config.js and .env files in your test environment
  dir: './',
});

// Add any custom config to be passed to Jest
const customJestConfig = {
  // Add more setup options before each test is run
  // setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  // if using TypeScript with a baseUrl set to the root directory then you need the below for alias' to work
  moduleDirectories: ['node_modules', '<rootDir>/'],
  testEnvironment: 'jest-environment-jsdom',
};

// createJestConfig is exported this way to ensure that next/jest can load the Next.js config which is async
module.exports = createJestConfig(customJestConfig);
```

### Add Unit Test

Now we will add out first unit test for the landing page. We will create a folder in the root directory for all of our tests. Jest uses the following convention of a `__tests__` directory for storing all of your Jest tests. We will create this directory and add a test file called `index.test.js` for our test. Add the following code to this file:

```jsx
// __tests__/index.test.js

import { render, screen } from '@testing-library/react';
import Home from '../pages/index';
import '@testing-library/jest-dom';

describe('Home', () => {
  it('renders a heading', () => {
    render(<Home />)

    const heading = screen.getByRole('heading', {
      name: /welcome to next\.js!/i,
    })

    expect(heading).toBeInTheDocument()
  })
});
```

### Add a Test Runner to package.json

Our last step will be to modify our `package.json` file to add a test runner script to the scripts section. My scripts section look like the following example:

```jsx
...
  "scripts": {
    "test": "jest"
  },
...
```

### Run the test

```bash
npm test
```

### Test Coverage

```jsx
npm jest --coverage

// You must put -- before passing the --coverage argument of Jest
// if you try invoking the --coverage directly without the -- it won't work

npm test -- --coverage
```

# Code Examples of testing

## Testing Asynchronous Code

It's common in JavaScript for code to run asynchronously. When you have code that runs asynchronously, Jest needs to know when the code it is testing has completed, before it can move on to another test. Jest has several ways to handle this.

```jsx
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});
```

## **`.mock` property**

All mock functions have this special `.mock` property, which is where data about how the function has been called and what the function returned is kept. The `.mock` property also tracks the value of `this` for each call, so it is possible to inspect this as well:

```jsx
const myMock1 = jest.fn();
const a = new myMock1();
console.log(myMock1.mock.instances);
// > [ <a> ]

const myMock2 = jest.fn();
const b = {};
const bound = myMock2.bind(b);
bound();
console.log(myMock2.mock.contexts);
// > [ <b> ]
```

```jsx
// The function was called exactly once
expect(someMockFunction.mock.calls).toHaveLength(1);

// The first arg of the first call to the function was 'first arg'
expect(someMockFunction.mock.calls[0][0]).toBe('first arg');

// The second arg of the first call to the function was 'second arg'
expect(someMockFunction.mock.calls[0][1]).toBe('second arg');

// The return value of the first call to the function was 'return value'
expect(someMockFunction.mock.results[0].value).toBe('return value');

// The function was called with a certain `this` context: the `element` object.
expect(someMockFunction.mock.contexts[0]).toBe(element);

// This function was instantiated exactly twice
expect(someMockFunction.mock.instances.length).toBe(2);

// The object returned by the first instantiation of this function
// had a `name` property whose value was set to 'test'
expect(someMockFunction.mock.instances[0].name).toBe('test');

// The first argument of the last call to the function was 'test'
expect(someMockFunction.mock.lastCall[0]).toBe('test');
```
