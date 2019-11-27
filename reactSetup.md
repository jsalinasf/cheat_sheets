# How to install react

For better instructions follow [React Official Documentation](https://reactjs.org/tutorial/tutorial.html)

1. Make sure Node.js is installed
1. npx create-react-app my-app  (this command wil create a my-app folder)
1. Go to the path: my-app/src and delete all of its content (rm -rf *)
1. add a file named index.css in the src/ folder
1. add a file named index.js in the src/ folder
1. add the following lines at the top of the index.js file

```javascript
import React from 'react';  
import ReactDOM from 'react-dom';  
import './index.css';
```

Finally, run npm start in the project folder and open http://localhost

