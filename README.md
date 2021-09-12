# I suffered so you don't have to
A list of gotchas that I've encountered while learning React.

# Props are strings
If you pass in a prop like so:
```
    ReactDOM.render(
        <RaceTrack lanes="5"/>,
        document.getElementById('race-track')
    )
```

Your constructor will recieve `lanes` as a string literal.
This means that if you then try to do something that requires an integral type, it will
likely fail spectacularly.

```
class RaceTrack extends React.Component {
    constructor(props){
        super(props);
        /** This will give you ["5"] */
        this.lanes = Array(props.lanes);
        
        /** This will loop 5 times */
        for(let i=0; i < props.lanes;i++){
            this.lanes.push();
        }
    }
}
```

## The fix?
Use `parseInt`
```
    constructor(props){
        super(props);
        /** This will give you 5 empty array items */
        this.lanes = Array(parseInt(props.lanes));
    }
```

# Lists require `key` attribute to be set
I feel like this should be a compile time error that your `npx` watch command should
catch, but the only way you'll run into this issue is after everything has compiled
and made it to the browser. 

I'm not past the point of realizing I might be wrong in assuming that React doesn't
warn you about this prior to browser rendering. If I am indeed wrong, please let me
know.

```
    constructor(props){
        super(props);
        this.lanes = Array(parseInt(props.lanes)).map(_ => 
            <Lane/>
        );
    }
```

This code will tell you in your js console:
```
Warning: Each child in a list should have a unique "key" prop.
```

Oh, right... I *totally* knew that.

But it's actually a non-issue, because the documentation tells you about it.
So, moot point I suppose on this one...

## The fix?
*OKAY* React. You win...
```
    constructor(props){
        super(props);
        this.lanes = [...Array(parseInt(props.lanes)).keys()].map(i => 
            <Lane key={i}/>
        );
    }
```

# Camelcase is not the right case (lol)
Trying to write classless CSS, and trying to be fancy, I created a `<verticalPipe>` element.
Yeah, React doesn't like that. It'll give you a warning. It wants `<vertical-pipe>` or
`<verticalpipe>` for html elements.

This might actually be a good thing and/or a standard.


# Camelcase is the *right* case for events
Events like `onclick` do not translate to React. You *must* use camel case for events:
```
    render(){
        /**
         * This won't even render. Inspect the element. onclick is nowhere to be found
         */
        return <button onclick={freakOut}>
            Freak out
        </button>
    }
```

## The fix?
```
    /** Notice the upper case 'C' */
    return <button onClick={freakOut}>
        Freak out
    </button>
```

# Returning false from an event handler will not work
Have you ever returned false from an input of type `submit`? We've all done it once or twice...
```
    <input type="submit" onclick="return validateForm();"/>
```

Your first instinct when porting this code to React might be to...
```
    validateForm(){
        let good = false;
        // ... some logic ...
        if(!good){
            return false;
        }
    }
    render(){
        return <input type="submit" onClick={this.validateForm}/>
    }
```

This is actually where I side with React on this. React has created what they call
'synthetic events'. It's like a normal event with things you'd expect (like event.preventDefault()).
So that actually happens to be our fix...

## The fix?
```
    validateForm(e){
        let good = false;
        // ... 
        if(!good){
            e.preventDefault();
            return;
        }
    }
```

React has went through the trouble of making events cross platform by using this pattern.
Where React loses me, is when your `validateForm` function is called, it does not 
have access to the `this` object. To work around that, you have to bind the function...
I won't be going over that, but if you'd like to know more, just go here: [React JS -> Handling Events](https://reactjs.org/docs/handling-events.html)

