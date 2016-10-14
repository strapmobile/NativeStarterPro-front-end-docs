# App structure and state management

If you are not familiar with MobX, have a look at these [docs](https://mobxjs.github.io/mobx/index.html) to get a basic idea about it.

### 1. Components
- #####AppNavigator
  - renderScene
    - get last route in AppViewStore.routeStack
    - ```js
      switch (lastRoute) {
           case ‘login’:
           return <Login />;
          ...
      }
      ```
  - componentWillReact
    - open or close Drawer in response to the change in value of AppViewStore.drawerOpened
  - loadInitialState
    - fetch TOKEN_KEY from AsyncStorage
    - If token found, fetch user details and send to dashboard. Otherwise, show Login page.
- #####Login
- #####Signup
- #####Home

### 2. Services
- #####AuthService
  - login()
    - Calls login endpoint using fetch API
    - Returns a promise
    - Resolves the promise on success
    - Rejects the promise on error
  - register()
    - Calls register endpoint using fetch API
    - Returns a promise
    - Resolves the promise on success
    - Rejects the promise on error

### 3. Models
- #####UserModel
  - Constructor ({email, token})
  - Properties
    - email
    - token

### 4. Stores

- ##### Entity Stores
  - AuthStore (file)
    - Properties (@observable)
      - user

    - Computed
      - isLoggedIn()

    - Actions
      - setUser(user)
        - sets this.user = user
      - unsetUser()
        - sets this.user = null

- ##### View stores

  - AppViewStore
    - Properties(Observable)
      - routeStack (use the structure of NavigatorExperimental)
      - drawerStatus
    - Methods(Actions)
      - pushRoute(route)
        - push route to routeStack array
      - popRoute()
        - pop from routeStack array
      - replaceRoute(route)
        - pop from routeStack array
        - push route to routeStack array

  - LoginViewStore
    - Properties (Observable)
      - email
      - password
      - isSubmitted
      - isSubmitting
      - isValid
      - validationErrors
      - apiSuccess
      - apiErrors

    - Methods (Actions)
      - validate()
        - Checks this.email and this.password and returns a JSON object with tested validations.
        - Use Validate.js here for easier approach
        - Doesn’t modify any value in the store but just returns
      - submit()
        - changes isSubmitted = true
        - changes isSubmitting = true
        - calls validate()
          - sets validationErrors = validate(...) 	// Validate.js method
          - sets isValid to true or false
        - if(valid) makes AJAX call using AuthService.login()
          - onSuccess:
            - sets AuthStore properties using UserModel (AuthStore.setUser(new User(...))
            - Call AppViewStore.replaceRoute(homeRoute)
          - onFailure:
            - sets apiSuccess to false
            - populate apiErrors if false
          - onFinally:
            - changes isSubmitting = false

  - SignUpViewStore
    - Properties (Observable)
      - email
      - name
      - password
      - reTypePassword
      - isSubmitted
      - isSubmitting
      - isValid
      - validationErrors
      - apiSuccess
      - apiErrors

    - Methods (Actions)
      - validate()
        - Checks this.username, this.password, this.reTypePassword and returns a JSON object with tested validations.
        - Use Validate.js here for easier approach
        - Doesn’t modify any value in the store but just returns
      - submit()
        - changes isSubmitted = true
        - changes isSubmitting = true
        - calls validate()
          - sets validationErrors = validate(...) 	// Validate.js method
          - sets isValid to true or false
        - makes AJAX call using AuthService.register()
          - onSuccess:
            - sets AuthStore properties using UserModel (AuthStore.addUser(new User(...))
            - Call AppViewStore.replaceRoute(homeRoute)
          - onFailure:
            - sets authValid to true or false
            - sets authErrors if false
          - onFinally:
            - changes isSubmitting = false