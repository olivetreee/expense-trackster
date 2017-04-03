# ExpenseTrackster

## Tech Stack and Architecture
**Backend**: Ruby on Rails and PostgreSQL

On the backend, a Rails MVC framework is configured to receive and respond to CRUD requests through API endpoints, making it agnostic to the frontend technology. The responses are formatted as JSON.

One static page is configured as html.erb, having the header and a single HTML element called `<div id="root">`. This will be the target that the frontend will look for.


**Frontend**: JS ES6 and React

A React component called `Root` gets rendered on the target `<div id="root">`. That component then renders other components (login or dashboard) according to the URL route.

## Backend Structure

### Auth
Authentication was written using the following framework:
* backend receives username (email) and password as plain text;
* the password is validated (min. 6 chars) and then hashed using the BCrypt gem, generating a password_digest;
* a model of a User with the username and password_digest is created;
* if it's a valid user (non-existent and with valid credentials), it gets created and commited to the DB. If not, an error is raised;
* on login, the input password is compared against the password_digest using the `BCrypt#is_password?(password)` method;
* if login is successful, a session_token is generated and saved to the respective User and stored in the client's cookie: `session[:session_token] = user.session_token`;
* the pure text password is, therefore, never stored anywhere and is discarded as soon as the login process finalizes with either success or error.

## Testing

### Backend

`bundle exec rspec` will run all specs.
`bundle exec rspec specs/user_spec.rb` will run the User model spec.
`bundle exec rspec specs/user_spec.rb:47` will only run the spec on line 47 of the User model.

Backend tests cover the following:

#### Models
|Model|Method|Test|
|--|--|--|
|User||Validates input param presence (`e-mail`, `password` and `is_admin` flag)|
|User||Accepts valid params|
|User||Validates `password` length|
|User||Does not save password to DB|
|User||Password is encrypted using BCrypt|
|User||Assigns a session_token|
|User||Finds user through credentials|
|User|find_by_credentials|Does not find user with incorrect password|
|User|find_by_credentials|Does not find user with incorrect email|
|Expense||Validates input param presence (`amount`, `datetime`, `description` and `owner_id`)|
|Expense||Validates `amount` digits|
|Expense||Accepts valid params|

#### Controllers
|Controller|Method|Test|
|--|--|--|
|Application||CSRF protection is set up|
|Users|show|Does not display user info when not logged in|
|Users|show|Displays user info to self|
|Users|show|Does not display another user's info, even if admin|
|Users|create|POST with valid parameters (user and admin)|
|Users|create|POST automatically logs in the user|
|Users|create|POST does not allow duplicate users|
|Users|create|POST fails with invalid parameters|
|Session|create|POST returns 404 with non-existing user|
|Session|create|POST returns 404 with incorrect password|
|Session|create|POST renders the user info with correct params|
|Session|create|POST logs in the user with correct params|
|Session|destroy|DELETE logs out the current user|
|Expenses|index|Does not display user info when not logged in|
|Expenses|index|Displays all expenses to admin|
|Expenses|index|Displays correct data to admin (`id`, `amount`, `datetime`, `description`, `owner_id`)|
|Expenses|index|Displays only self expenses to user|
|Expenses|index|Displays correct data to user (`id`, `amount`, `datetime`, `description`, `owner_id`)|
|Expenses|show|Does not display user info when not logged in|
|Expenses|show|Displays to admin an expense created by self|
|Expenses|show|Displays to admin an expense created by user|
|Expenses|show|Displays to user an expense created by self|
|Expenses|show|Does not display to user an expense created by another user|
|Expenses|create|Does not create new expenses when not logged in|
|Expenses|create|Creates new expenses with valid params, both as user and admin|
|Expenses|update|Does not update when not logged in|
|Expenses|update|Updates own expense with valid params as admin|
|Expenses|update|Does not update another user's expense as admin|
|Expenses|update|Does not update expense with invlid params as admin|
|Expenses|update|Updates own expense with valid params as user|
|Expenses|update|Does not update another user's expense as user|
|Expenses|update|Does not update expense with invlid params as user|
|Expenses|destroy|Does not delete expense when not logged in|
|Expenses|destroy|Deletes own expense as admin|
|Expenses|destroy|Does not Delete another user's expense as admin|
|Expenses|destroy|Deletes own expense as user|
|Expenses|destroy|Does not Delete another user's expense as user|
