# Advanced Topics with React and Redux

The project serves as a sandbox playground to learn various advanced topics such as redux-thunk, firebase, redux-form, animation, deployment, redux data storage, and more

***

[Installation](https://github.com/chris-malloy/advanced-topics-react#installation) | [Testing](https://github.com/chris-malloy/advanced-topics-react#testing) | [Challenges and Solutions](https://github.com/chris-malloy/advanced-topics-react#challenges-and-solutions) | [Code Snippets](https://github.com/chris-malloy/advanced-topics-react#code-snippets) | [Acknowledgements](https://github.com/chris-malloy/advanced-topics-react#acknowledgements)

***

## Installation

OS X & Linux:

```sh
npm install
```

## Testing

Snapshot testing is done with Jest and assertion testing is done with Enzyme.

```sh
npm install --save-dev jest babel-jest babel-preset-env babel-preset-react react-test-renderer
```

```sh
npm i --save-dev enzyme enzyme-adapter-react-16
```

### Topics

#### Redux-thunk

Redux thunk is middleware between your redux store and API calls. It allows your action creators to return funcitons which you can dispach as needed. It does this by giving you more control over redux's dispatch method.

Below is an example of making an axios request, and then instead returning with a promise like redux promise would, redux thunk expects to you to return with a function.  Once .then resolves, redux dispatch method will get called. This can be useful if you need to affect the data before the dispatcher sends it to the reducers.

```javascript
export function fetchUsers() {
  const request = axios.get('http://jsonplaceholder.typicode.com/users');

  return (dispatch) => {
    request.then(({data}) => {
      dispatch({ type: 'FETCH_PROFILES', payload: data })
    });
  };
}
```

#### Firebase/Redux Integration

Firebase is API that updates state by waiting for value events. This is different from redux, which updates state by waiting for action creators to trigger. To combine the two libraries, the value events need to be placed inside of the action creators.

You will first create a snapshot of your data like so:

```javascript
const Posts = new Firebase('https://fbredux.firebaseio.com/');
```

If you want to write an action creator to fetch the data inside of Posts, you might write it like this:

```javascript
export function fetchPosts() {
  return dispatch => {
    Posts.on('value', snapshot => {
      dispatch({
        type: FETCH_POSTS,
        payload: snapshot.val()
      });
    });
  };
}
```

An action creator like fetchPosts is written like normal, then we return a dispatch (redux-thunk) which resolves by grabing a snapshot from Posts. This comes from the firebase documentation.

This approach gets interesting when you create two more actions that might make sense with a fetch data action, create new data, and delete data:

```javascript
export function createPost(post) {
  return dispatch => Posts.push(post);
}

export function deletePost(key) {
  return dispatch => Posts.child(key).remove();
}
```

Notice that no payload is returned. Meaning that there really doesn't need to be a reducer that listens for the createPost and deletePosts action creators. The fetchPosts action is the only one that needs to reutrn some payload, because the firebase value event is going to trigger whenever firebase's data is changed. In this way you can leverage the power of firebase's API, and still use redux to manage your application state.

### Acknowledgements

Stephen Grider, thank you for your course.  It was a great way to take my React knowledge to the next level.
