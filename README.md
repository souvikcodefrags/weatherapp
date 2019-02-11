# WeatherApp

### /index.js

```sh
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore, applyMiddleware } from 'redux';
import  ReduxPromise from 'redux-promise';

import App from './components/app';
import reducers from './reducers';

const createStoreWithMiddleware = applyMiddleware(ReduxPromise)(createStore);

ReactDOM.render(
  <Provider store={createStoreWithMiddleware(reducers)}>
    <App />
  </Provider>
  , document.querySelector('.container'));
```

### /actions/index.js

```sh
import axios from 'axios';

const API_KEY = '710486ede3c1b3a27559af4c487bb598';  
const ROOT_URL = `http://api.openweathermap.org/data/2.5/forecast?appid=${API_KEY}`;

export const FETCH_WEATHER ='FETCH_WEATHER'

export function fetchWeather(city) { console.log(city)
    const url = `${ROOT_URL}&q=${city},us`;
    const req = axios.get(url)
    return {
	type: FETCH_WEATHER,
	payload: req
    }

}
```

### /components/app.js

```sh
import React, { Component } from 'react';
import WeatherList from '../containers/weather_list'
import SearchBar from '../containers/search_bar'

export default class App extends Component {
  render() {
    return (
      <div>
	<SearchBar />
	<WeatherList />
      </div>
    );
  }
}
```

### /containers/search_bar.js

```sh
import React, { Component } from "react";
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import { fetchWeather } from '../actions/index'


class SearchBar extends Component{
    constructor(props){
	    super(props)
	    this.state = {term: ''}
    }

    handleInputChange = (event) => {
	    this.setState({term: event.target.value})
    }

    handleSubmit = (e) => {
    	e.preventDefault()
    	this.props.fetchWeather(this.state.term)
    	this.setState({term:''})
    }

    render(){
    	return(
    	    <div>
    		<form action="" className="input-group" onSubmit={this.handleSubmit}>
    		    <input type="text"
    			placeholder="Get a five-day forecast in your favorite cities"
    			className="form-control"
    			value={this.state.term}
    			onChange={this.handleInputChange}
    		    />
    		    <span className="input-group-btn">
    			<button type="submit" className="btn btn-secondary">
    			Submit
    			</button>
    		    </span>
    		</form>
    	    </div>
    	)
    }
}

//function mapDispatchToProps (dispatch)
//{
    //return bindActionCreators( {fetchWeather}, dispatch)
	//return fetchWeather
//}


//export default connect (null, mapDispatchToProps)(SearchBar)
export default connect (null, { fetchWeather })(SearchBar)
```

### /containers/weather_list.js
```sh
import React, { Component } from "react";
import { connect } from 'react-redux';

class WeatherList extends Component{
    renderWeather(cityData){
    	console.log(cityData);
    		return (
    	    <tr key={cityData.city.name}>
    		<td>{cityData.city.name}</td>
    		<td>{cityData.list[0].main.temp}</td>
    		<td></td>
    		<td></td>
    	    </tr>
    	)
    }
    render(){
    	return(
    	    <table className="table table-group">
    		<thead>
    		    <tr>
    			<th>City</th>
    			<th>Temparature</th>
    			<th>Pressure</th>
    			<th>Humidity</th>
    		    </tr>
    		</thead>
    		<tbody>
    		    {this.props.weather.map(this.renderWeather)}
    		</tbody>
    	    </table>
    	)
    }
}

const mapStateToProps = ({weather})=>( {weather} )
export default connect(mapStateToProps)(WeatherList)
```

### /reducers/index.js
```sh
import { combineReducers } from 'redux';
import WeatherReducer from './reducer_weather'

const rootReducer = combineReducers({
  weather: WeatherReducer
});

export default rootReducer;
```


### /reducers/reducer_weather.js
```sh
import { FETCH_WEATHER } from "../actions/index";

export default function (state=[], action){
    switch(action.type){
    	case FETCH_WEATHER:
    	    return [action.payload.data, ...state];
        }
    return state;
}
```
