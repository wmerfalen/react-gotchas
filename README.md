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
