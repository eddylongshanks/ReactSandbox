The full course: https://app.pluralsight.com/library/courses/react-18-fundamentals

Source code: https://github.com/RolandGuijt/ps-react-fundamentals

**Table of Contents**
- [Anatomy of an Application](#anatomy-of-an-application)
    - [Components](#components)
    - [Modules](#modules)

---
# Anatomy of an Application

## Components

```javascript
const Banner = () => <h1>This is a banner</h1>;
```

js library "reactdom" renders the component as an html element

its possible to render a component using another component inside it

```javascript
const Greeting = () => (
    <div>
        <Banner />
        <h2 className="highlight">Greetings!</h2>
    </div>
);
```

div and h2 are technically also components, but theyre built in and the reactdom recognises it and renders the appropriate html tag

built in components are camelCase, user created components are PascalCase

it is recommended to use function components instead of class components

App is generally the "parent" component. Everything is built with components and sub-components. 

## Modules

To make a file into a module you need to "export" something

```javascript
const doSomething = () => {
    ...
}
export { doSomething };
```

This lets other modules import and use it. Other functions inside the module that arent exported are (seemingly) "private".

Import a function from one file into another by using "import", specify the filename without the extension:

```javascript
import { doSomething } from "./module";

doSomething();
```

You can export a default function from a file by specifying `export default doSomething` and then this can be imported as any name `import do from "./module";`

### Why use modules?

- They bring strucuture
- Allows reusability
- Encapsulation - anything not exported remains private
- Needed for bundling, the tool creates one big file

Its good practice to have one public function per file. The function can only have one parent element, `<header>` in this case.

```js
const Banner = () => {
    return (
        <header>
            <div> Some Text</div>
        </header>
    );
};

export default Banner;
```

Then to use this in another file

```js
import Banner from "./banner";

const App = () => {
    return <Banner />
};

export default App;
```

# Hooks, Props, and State

## Props

-Passing arguments to components
-the receiving component can access the props object
-Allows for reusability

Parameters can be added into a function call

```javascript
import Banner from "./banner";

const App = () => {
    return <Banner headerText="Hello this is a header" />
};

export default App;
```

Then the "headerText" can be accessed via any name, but the convention is to use "props", the "headerText" is now automatically available in the "props" object:

```javascript
const Banner = (props) => {
    return (
        <header>
            <div>{props.headerText}</div>
        </header>
    );
};

export default Banner;
```

Note: props is readonly. The component receiving props is not allowed to change them, to avoid problems down the line.

The props object can also be "destructured" in order to only obtain a certain set of properties from the overall object:


```javascript
const Banner = ({headerText}) => {
    return (
        <header>
            <div>{headerText}</div>
        </header>
    );
};
```

## The Children Prop

It is possible to access the children of a component, the stuff contained within the tags:

```javascript
const App = () => {
    return (
        <div>
            <Banner>
                <span>
                    This is the data that will be used by the Banner component, including the span element
                </span>
            </Banner>
        </div>
    );
};
```

The data inside the banner tag will be accessed via the following:

```javascript
const Banner = ({children}) => {
    return (
        <header>
            <div>{children}</div>
        </header>
    );
};
```

Html can also be stored inside any variable too:

```javascript

const thing = <div>hello</div>

return (
    <div>
        {thing}
        <Banner>The Banner stuff</Banner>
    </div>
);
```

## Fragments and Mapping Data

### Using Fragments "<>"

Since there can only be one parent element in a function/component, a `<div>` could be specified. But if you dont want to have a random `<div` rendered on the page then you can use `<React.Fragment>`. Shorthand for this is to use an empty node: `<>` & `</>`:

```javascript
const HouseList = () => {
    return (
        <>
            <div className="row mb-2">
                <h5>The list of Houses</h5>
            </div>
        </>
    );
};
```

### Mapping Data

See the following example for mapping an array and rendering it in a table:

```javascript
const houses = [
    {
        id: 1,
        address: "12 House lane, houseville",
        country: "UK",
        price: 130000
    },
    {
        id: 2,
        address: "16 House lane, houseville",
        country: "UK",
        price: 150000
    },
];

const HouseList = () => {
    return (
        <>
            <table>
                <thead>
                    <th>Address</th>
                    <th>Country</th>
                    <th>Price</th>
                </thead>
                <tbody>
                    {houses.map((h) => (
                        <tr key={h.id}>
                            <td>{h.address}</td>
                            <td>{h.country}</td>
                            <td>{h.price}</td>
                        </tr>
                    ))}
                </tbody>
            </table>
        </>
    );
};
```

Then as shown earlier, this component can be used as follows:

```javascript
import HouseList from "./houselist";

const App = () => {
    return (
        <>
            <HouseList/>
        </>
    );
};
```

## Extracting Components

The above example can be simplified by abstracting the row rendering, a new file and function would be created, `houseRow.js`:

```javascript
const HouseRow = ({house}) => {
    return (
        <tr>
            <td>{house.address}</td>
            <td>{house.country}</td>
            <td>{house.price}</td>
        </tr>
    );
};
```

The `HouseList` function would then be changed to reflect the following:

```javascript
...
    <tbody>
        {houses.map(h => <HouseRow key={h.id} house={h} />)}
    </tbody>
...
```

Instead of having to access house property directly, it can be destructured:

```javascript
const HouseRow = ({ house: {address, country, price} }) => {
    return (
        <tr>
            <td>{address}</td>
            <td>{country}</td>
            <td>{price}</td>
        </tr>
    );
};
```

You dont even need to specify the "house" property:

```javascript
const HouseRow = ({ address, country, price }) => {};
```

Which are passed through as:

```javascript
...
    <tbody>
        {houses.map(h => <HouseRow key={h.id} address={h.address} county={h.country} />)}
    </tbody>
...
```

This can become a long list, so instead, you can use a "spread" property, which will unpack all the properties and pass them in as individuals:

```javascript
...
    <tbody>
        {houses.map(h => <HouseRow key={h.id} {...h} />)}
    </tbody>
...
```

Note: Be careful here as it will pass through all properties, even ones added in future, it isn't "future proof", best to be specific and not lazy!

Over all it would generally be better to pass in the `house` property as shown at the beginning of this [section](#extracting-components). This is the most clear and specific way of accessing all properties needed.

## Hooks

- A function
- Starts with "use"
- Encapsulates complexity
- Rule 1: Only call hooks at the top level (top of the function)
    - They can't called conditionally (wrapped in if statement)
    - Must be called in the same order every time the function is executed
- Rule 2: Only call hooks in function components, unless its a custom hook

### State (a common hook)

When using the example in the previous section, there is a one way data flow; an array is created in a function or on page, the map function maps that to a component (`HouseRow`) and then is displayed in the dom in a table. React doesn't know when an object is added to the original array. Hooks do this:

```js
const [houses, setHouses] = useState(houseArray);
```

The original array is passed in to `useState()`, a hook which is a built in function; it is destructured to return an array, in this case an object that represents the current state `houses` and `setHouses`, a function that we can use to change the state. When `setHouses` is called, react will re-render the ui if needed.

```javascript
const houseArray = [
    {
        id: 1,
        address: "12 House lane, houseville",
        country: "UK",
        price: 130000
    },
    {
        id: 2,
        address: "16 House lane, houseville",
        country: "UK",
        price: 150000
    },
];

const HouseList = () => {
    const [houses, setHouses] = useState(houseArray);
    return (
        <>
            <table>
                <thead>
                    <th>Address</th>
                    <th>Country</th>
                    <th>Price</th>
                </thead>
                <tbody>
                    {houses.map((h) => (
                        <tr key={h.id}>
                            <td>{h.address}</td>
                            <td>{h.country}</td>
                            <td>{h.price}</td>
                        </tr>
                    ))}
                </tbody>
            </table>
        </>
    );
};
```

### Setting State

```js
const HouseList = () => {
    const [houses, setHouses] = useState(houseArray);    
    
    const addHouse = () => {
        // Don't change 'houses' directly here, treat it like a private field, accessed only via the setHouses function
        setHouses([
            // This adds the existing array to the new array
            ...houses,
            {
                id: 3,
                address: "16 house lane, houseville",
                country: "UK",
                price: 190000
            },
        ]);
    };

    return (
        <>
            <table>
                <thead>
                    <th>Address</th>
                    <th>Country</th>
                    <th>Price</th>
                </thead>
                <tbody>
                    {houses.map((h) => (
                        <tr key={h.id}>
                            <td>{h.address}</td>
                            <td>{h.country}</td>
                            <td>{h.price}</td>
                        </tr>
                    ))}
                </tbody>
            </table>
            <button onClick={addHouse}>
                Add
            </button>
        </>
    );
};
```

Another example of a simple function that could be added:

```js
const [counter, setCounter] = useState(0);
setCounter(current => counter + 1);

// 'current' is equal to the value of counter
```

**Recap**
- State is private data in a component, managed by that component
- `useState()` takes the initial value of the state as a parameter
- Returns an array containing the current value, and a function to change that value (the "set" function)
- Change the value using the 'set' function only



# Conditional Rendering and Shared State

## Conditional Rendering

Variables can be used to confitionally render html:

```js
const HouseRow = ({ house }) => {
    let priceTd;
    if (house.price < 500000)
        priceTd = <td>{house.price}</td>
    else
        priceTd = <td className="text-primary">{house.price}</td>
    return (
        <tr>
            <td>{house.address}</td>
            <td>{house.country}</td>
            {priceTd}
        </tr>
    );
};
```

The above can be shortened to an expression with a template literal:

```js
const HouseRow = ({ house }) => {    
    return (
        <tr>
            <td>{house.address}</td>
            <td>{house.country}</td>
            <td className={`${house.price >= 500000 ? "text-primary" : ""}`}>
                {house.price}
            </td>
        </tr>
    );
};
```

This can be modified further to only render the price if the value is "truthy", meaning is isnt null or zero, roughly c# equivalent of a null coalesce. This works because javascript will check the first part of the operator and if false, won't look at the second part:

```js
const HouseRow = ({ house }) => {    
    return (
        <tr>
            <td>{house.address}</td>
            <td>{house.country}</td>
            {house.price && (
                <td className={`${house.price >= 500000 ? "text-primary" : ""}`}>
                    {house.price}
                </td>
            )}
        </tr>
    );
};
```

A similar exaple is below that determines whether an image is defined before attempting to render in ```<img>``` element:

```js
import defaultPhoto from "../helpers/defaultPhoto";

<img src={house.photo ? `./houseImages/${house.photo}.jpg` : defaultPhoto} alt="House pic" />
...
```

## Conditionally Rendering Components

Using ```useState()```

```js
const App () => {
    const [selectedHouse, setSelectedHouse] = useState();
    return (
        <>
            <Banner>
                <div>Providing houses all over the world</div>
            </Banner>
            {selectedHouse ? <House house={selectedHouse} /> : <HouseList />}
        </>
    );
};
```

- ```selectedHouse``` will initially be undefined because no value has been passed to ```useState```
- Right now, the state will never change because ```setSelectedHouse``` is never called

## Passing in functions as Props and Determining State Location

Given the above code:
Problem: The ```App``` can't know when a ```House``` is selected as that component is multiple rows down in the code. Capturing that event is not an option, instead this can be passed down through HouseList as a property so that it can set the state for App:

```js

{selectedHouse ? <House house={selectedHouse} /> : 
    <HouseList selectHouse={setSelectedHouse} />}

```

Note: The code above will not work, when moving on to a new line with a conditional statement, it must be wrapped in ():

```js
<>
    {selectedHouse ? (
        <House house={selectedHouse} />
    ) : (
        <HouseList selectHouse={setSelectedHouse} />
    )}
</>
```

In order for HouseList to then use this property, it must be destructured:

this:

```js
const HouseList = () => {
    ...
}
```

becomes this:

```js
const HouseList = ({ selectHouse }) => {
    ...
}
```

We won't use this in HouseList itself, it will pass down through HouseRow:

```js
<HouseRow key={h.id} house={h} selectHouse={selectHouse} />
```

Note that the property being passed in this instance is ```selectHouse```, the property that was received by ```HouseList```, it is being passed deeper as the same name: ```selectHouse```

Then the HouseRow will destructure the property:

```js
const HouseRow = ({ house, selectHouse }) => {
    return (
        <tr onClick={() => selectHouse(house)}>
        ...
    )
}
```

Summing up:
Setting the state of whether a House or HouseList was shown was set at the top level of App; this was passed as a prop to HouseList, and then passed again down to HouseRow. This allowed HouseRow to change the state at the App level, since this function that is passed is a reference to the parent function, not a copy.

Notes on setting State:
- Passing functions as properties creates a reference, not a copy
- In this case the function changes state of the whole application, generally its best practice ot to do this unless needed
- Be mindful of WHERE you NEED to change the state, start at the bottom (HouseRow), if its parent needs to have its state changed, then introduce the set function there and pass it down to its child HouseRow, and so on.
  - Essentially, place the state as low as you can

## Mounting and Unmounting

When mounting and unmounting components (as above), be aware that their state is lost when unmounting.

## Function Wrappers and the callback hook

### Function Wrapper

When sending a function down into child components, it gives that component power to send back whatever it wants, not just a "House" component. To avoid this, a wrapper can be created that is passed instead, this would take responsibility of asserting that a valid object is being passed back up to the parent:

```js
const setSelectedHouseWrapper = (house) => {
    //do checks to determine whether the object is valid
    setSelectedHouse(house);
}

...

<>
    {selectedHouse ? (
        <House house={selectedHouse} />
    ) : (
        <HouseList selectHouse={setSelectedHouseWrapper} /> //Send wrapper instead
    )}
</>
```

Caveats:
- The function object is recreated and a new reference is used (think, a new singleton)
- Watch out for this when using memoised components

### Callbacks

If you need to preserve the same function reference between renders than a callback can be used, this memoises the contained function, and can be included inside the wrapper:

```js
const setHouseWrapper =
    useCallback((house) => {
        setSelectedHouse(house);
    },[]);
```

## Delegating State to a Custom Hook

Custom hooks 

- Should always have the "use" prefix in their name