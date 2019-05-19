<h1 align="center"><strong>SaaS-GraphQL, a SaaS GraphQL Boilerplate</strong></h1>

<br />

<div align="center"><img src="https://github.com/davodesign84/saas-graphql/blob/master/logos.svg" /></div>

<div align="center"><strong>Start your Saas Project in a matter of seconds</strong></div>
<div align="center">Saas-Graphql allows you to get the basic scaffolding for your Saas Project up and running in seconds</div>

<br />

## Features

- **Serverless**: Using Zeit Now for deployment you can get your GraphQL API deployed in seconds. Zero maintenance and low cost. 🚀
- **Typesafe**: Typescript allows you to write better code faster. On top of that, due to the nature of graphql, all your query types can be generated as easily as `npx apollo client:codegen` for use on your favourite frontend tech stack. 🛡
- **Stateless**: Using the JWT allows for a stateless system, no need to keep track of session ids and the like on Redis, or on your database. Sweeeeeet 🍭
- **Easily extensible**: As a boilerplate Saas-GraphQL, provides the boring, annoying setup to get your saas started. Things like Accounts, Users, Authentication and Authorization. Leaving space for your customisations 👷
- **(Almost) 0 config**: The only things you need to do is provide your JWT token secret and your mongodb URI 🙀

## Quickstart
- Clone the project: `git clone https://github.com/davodesign84/saas-graphql` and `cd saas-graphql`
- Run `yarn` or `npm install` to install dependencies
- Install Zeit now, instructions can be found [here](https://zeit.co/download).
- add your `DATABASE_URI`  (for the mongodb connection) and `SECRET` (for the JWT generation) secrets to a `.env` file in your root folder
- Generate the token secret [here](https://mkjwk.org/) and add it to Now `now secret add SECRET <your token secret>`
- Add your mongodb URI `now secret add DATABASE_URI <your token secret>`
- Run dev `now dev` and open `localhost:3000`
- The schema can be explored on the right end side of the Graphql playground


Enjoy! You just saved a couple of days of faffing around with boring stuff 🤴


## Structure
At the core of Saas-GraphQL there are three concepts: Organization, Project and User.
An Organization is the bucket of users and projects, think of it as your client company.
A Project is like a workspace, a bucket where you can all the different activities of your client company, think of it as a Mixpanel project or a Trello board.
A User is a member of your client company. Each User can have one of three roles, OWNER, ADMIN and USER, where OWNER » ADMIN » USER.

## Create User and Login
To create a user:
```
mutation {
    createUser(input: {
        email: "test@test.com"
        password: "test"
        firstName: "John"
        lastName: "Doe"
    }) {
        id
    }
}
```

then login the user

```
mutation login {
  loginUser(input:{
    email:"test@test.com"
    password: "test"
  }) {
    jwt
  }
}
```

This will return you the JWT token and set the related cookie which needs to be attached to every subsequent call made to the GraphQL endpoint, as it is used to check permissions.
In Apollo-Client for example it can be done like so:
```
// with-apollo.ts

const client = new ApolloClient({
    cache: new InMemoryCache(),
    link: createHttpLink({
        uri: 'http://localhost:3000',
        credentials: 'include'
    }),
    ssrMode: true
});
```


## Permissions
Permissions for the members of an Organization are currently hard-coded (PRs for making it more flexible are more than welcome) but follow these simple rules:
 - *Owners* can do everything except for removing themselves if that leaves the Organization with no Owner;
 - *Admins* can do everything except
 - - Changing name of the organization
 - - Editing / Removing or Inviting Owners
 - - Editing / Removing any other ADMIN but themselves
 - *Users* can only remove themselves from an Organization

 Permissions are inferred from the User ID attached to the JWT token in the `Authorization` header.

 To edit members (and their relevant permissions) simply make a call to with the `editOrganization` operation:
 ```
 mutation edit {
  editOrganization(input:{
    organization: "<organizationID>"
    name: "Changed Name"
    members: [{
      role: OWNER
      user: "<userID>"
    },{
      role: ADMIN
      user: "<anotherUserID>"
    }]
  }) {
    name
    members {
      role
      user {
        id
      }
    }
  }
}
```

## Contributing

Your feedback is **very helpful**, please share your opinion and thoughts! If you have any questions or want to contribute yourself, please create an issue or feel free to contributes to PR directly, Jest tests would be very welcome! 🙏
