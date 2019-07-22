---
title: Clean Architecture in Action
date: {{ date }}
---


中文版本（Chinese Version）：《[Clean Architecture 实施指南](https://www.phodal.com/blog/clean-frontend-architecture-in-action/)》

In the previous article [Clean Frontend Architecture](https://phodal.github.io/clean-frontend/), we introduced how to use Clean Architecture in the frontend. In the past few months, we have implemented the Clean Architecture architecture and have proven that Clean Architecture can also work very well on the frontend.

## Clean Architecture + MVP + Componented-based Architecture

Before we get started, let's take a look at the final architecture of Angular apps that use Clean Architecture:

![Clean MVP + Componet-based + MVP](https://phodal.github.io/clean-frontend/docs/images/clean-mvp-component-based.jpg)

In the figure, we split the architecture for this part:

 - Data layer. That is, the processing layer of all frontend and backend interactions, from the request to the return result, only returns the value and field which **required by the front end**.
 - MVP layer. MVP is not the focus of this article, but interestingly, in Angular applications, the module layer can correspond to the backend service. The page under the module layer can also be split according to this.
 - The component layer in MVP. A reasonable planning of the component layer will make our componet layer clean, not just the domain layer. Components, the hard part is to meet the scene, not split, combine and package.

Here, we have less part of the style layer. As a result, the use of various CSS preprocessors to organize code is very mature; second, CSS has not hurt so much in today's infrastructure.

We have added more implementation details than our old architecture diagram:

![Clean Architecture](https://phodal.github.io/clean-frontend/docs/images/clean-frontend-components.jpg)

However, the former is qualified and the latter is the architecture under general conditions.

## Context of implementation

Clean Architecture is not a silver bullet, it is suitable for us, it does not mean it is suitable for you. Especially if you are used to free style and autonomous project development, then the strong standardization of Clean Architecture + Angular is not necessarily for you. However, at the same time, if your team is large and there are more junior developers, then I think normalization can help you reduce problems - easy to maintain.

So, let me introduce some contexts that are good for us:

 - Implementation of DDD's microservice backend architecture.
 - A member who is interested in implementing a full-stack team.

There are other Angular frameworks that are more suitable for the enterprise (pursuing standardization), such as the junior developers, so the specification is more, but better maintained. However, the remaining factors, for our architecture: helpful, but not too big.

### Implementing DDD's microservice backend architecture

DDD is just a set of software development methods, and different people understand the DDD self-differentiation. In a specific scenario, when DDD is used for microservice splitting, each subdomain is a specific microservice. Under this model, we need to have a naming pattern to distinguish each service, and one of them is a URL. Each service has its own unique URL prefix/path, and when these services are exposed, the corresponding front-end domain layer can be produced—even if it has not participated in an event storm or domain division.

For example: ``/api/payment/``, ``/api/manage/`` can clearly split the front ``domain data`` layer.

At the same time, if the backend then names the Controller according to the resource path, such as ``/api/blog/blog/:id``,``/api/blog/blog/category/:id``, then the front end can Clearly divide them into the same ``repository``. Of course, once the backend design has problems, the front end can be clearly noticed.

### Full-stack team

In the past, we built our team as full-featured team in a ThoughtWorks, each team members excel in a particular area, but in other areas, such as good at the frontend and can do backend. It can reduce communication costs to some extent. And that means we have a lot of cost for knowledge transfer. Therefore, we used pair programming to let new people talk about project-related sessions.

So, when you decide to become a full-featured team (front and back + business analysis), then you will encounter such problems:

 - Find the front-end code of the app, how to find it quickly?
 - Find the corresponding backend code,
 - Front and back model corresponding
 - ......

Keeping the front and back ends as consistent as possible becomes a new challenge.

## Directory as layered architecture

In a sense, Clean Architectute is an implementation of **normalization** and **templating**. At the same time, its three-layer mechanism in the data layer makes it exist in two layers of **anti-corrosion layer**, usecase can be used as a buffer layer for services, and the repository layer can isolate back-end services and models.

In many architectural designs, layered architecture is the easiest to implement because directories are layered. The catalog is a specification that you can see at a glance. Once misplaced, you can see it at a glance.

### MVP Stratification (Directory Partitioning)

From the catalog results, our division is not much different from the general Angular application.

```javascript
├── core           // core code, including basic services and basic code
├── domain         // domain layer code, containing separate Clean schema content for each domain
│ └── elephant     // a specific domain
├── features       // public domain components
├── presentation   // domain logic page
├── pages         // public page
└── shared        // shared directory
```

We will:

1. The business pages are placed in the ``presentation`` directory.
2. Public pages (such as 404) are placed in the ``pages`` directory.
3. The business components shared by the business page are placed in the ``features`` directory.
4. The remaining common parts are placed in the ``shared`` directory, such as ``pipes``, ``utils``, ``services``, ``components``, ``modules``.

Sample code can be found at: [https://github.com/phodal/clean-frontend](https://github.com/phodal/clean-frontend)

### domain + data Layer: Vertical + Horizontal Layered

The domain layer in the above directory, the example structure is as follows:

```javascript
├──model
│ ├── elephant.entity.ts       // Data entity, a simple data model used to represent the core business logic
│ └── elephant.model.ts        // core business model
├── repository
│ ├── elephant.mapper.ts       // Mapping layer for core entity layer mapping, or mapping to core entity layer. Model conversion
│ └── elephant.repository.ts   // Repository for reading and storing data.
└── usecases
    └── get-elephant-by-id-usecase.usecase.ts // Use cases, built on top of core entities, and implement the entire business logic of the application.
```

The relevant explanation is as above, here is not Ctrl + V / Ctrl + C.

It is worth noting that we are adopting a vertical + horizontal double layered model, and vertical response to domain services. It is suitable for microservices architectures without BFF, especially for microservices backend applications that use DDD.

## Mapping Domain Services

In the previous section, the front-end domain + layer layer actually mapped the backend services. When the current end initiates a request, its flow is generally like this:

> Component / Controller -> Usecase -> Repository -> Controller (backend)

The corresponding return order is: 

> Controller (backend) -> Repository -> Usecase -> Component / Controller (front end)

To do this, we correspond the Repository to the Controller on the back end. And because of the simplification of the service, most of our usecase also corresponds to the naming in the repository.

### repository naming: URL naming

To name it without looking at the backend code, we use URLs to name methods in the repository and repository. If there is one

| URL | Explanation | Abstract |
|------|-----|----------|
| ``/api/blog/blog/:id`` | /API/Micro Service Name/Resource Name/Resource ID | HTTP Verb + Resource + Behavior |

Ever since, the corresponding ``repository`` should be named ``blog.repository.ts``. The name of the corresponding repository is also ``get-blog-by-id``. Similarly, the repository corresponding to the URL ``/api/blog/blog/:id/comment`` is ``get-comment-by-blog-id``.

Well, yes, it is consistent with the access to the database.

### usecase naming

Since we don't involve complex APIs, the common behavior is as follows:

 - General verb: get / create / update / delete / patch
 - Unconventional: search, submit

Haha, is it similar to the repository.

## Clean Architecture's MVP layer practice

In fact, the MVP layer here, the main content is the component architecture. This part of the content has been in the previous article ( [Component-based Architecture's patterns](https://en.phodal.com/2019/07/16/experience-design-component-based-architecture/) ) introduced, not detailed here. A brief introduction is:

 - Basic component library, such as Material Design
 - Encapsulation package components. Additional three-party component libraries, such as Infinite Scroller, must be used after encapsulation.
 - Customize basic components.
 - Domain specific components.

The above four parts build a common component library module for the entire system. This is followed by domain-related components and page-level components.

 - Domain related components. Share logical components between different modules and pages.
 - Page level components. Share pages between different modules and routes.

Well, there are so many things in this part.

## Clean Architecture Domain + Data Layer Practice

Well, the other parts are not much different from normal project development. So we can focus on the Domain + Data layer.

### DDD ApplicationService vs Multiple Usecases

> In DDD practice, it is natural to adopt a top-down implementation. The implementation of ApplicationService follows a very simple principle, that is, a business use case corresponds to a business method on the ApplicationService. [^ddd_backend]

[^ddd_backend]: https://insights.thoughtworks.cn/backend-development-ddd/

A little different is that the Clean Architecture recommended by us is: **A business use case (usecase) corresponds to a business class**. That is, in the same business scenario, the frontend is a bunch of usecase files, and the back end is an applicationService. So, in this scenario, the frontend has them:

 - change-production-count.usecase.ts
 - delete-product.usecase.ts

The backend is ``OrderApplicationService.java``, which has multiple methods.

### usecases + repository vs services

If our usecases + repository does the same thing as a service, isn't it good to just use serivce? Using only service has this problem:

 - repeated API calls
 - Calling context not clearly defined

So, use usecase:

 - More templated code
 - More stratification

The reuse rate of Usecases is extremely low, and the project will dramatically increase the class and duplicate code. Therefore, we try to increase the maintainability of the architecture with more code. PS: More code, it may also reduce the maintainability of the code, but in today's intelligent IDE, this should not be a problem.

### Usecases as a logical layer / anti-corrosion layer

However, Usecases brings a layer of anti-corrosion while bringing more code. It is responsible for the following responsibilities:

 - Business logic processing. Process the necessary content before the data is passed to the back end.
 - Return to data management. From the data returned from the backend, build the results needed for the front end. This can be done in usecase when multiple APIs need to be called.
 - Input parameter management.

Therefore, the Usecases layer is very useful when the current end is given too much business logic. Conversely, if the logic is placed in the BFF layer, the Usecases layer becomes a bit sloppy. But it is still a very good anti-corrosion layer.

## Model Management

As we work with Usecase, we need to solve the frontend model problem. The backend has multiple microservices and multiple projects, each with its own model. And if there is only one project in the frontend, the frontend model management becomes a pain point. Because in different contexts, the backend model is different. That is to say, in different APIs, the models are different, and these models are customized according to the business, and finally aggregated together at the front end.

At this time, it is possible to have multiple different models for the same resource. So either:

 - The frontend has a one-to-one model. It is more troublesome to manage.
 - Use the same model. You cannot use type checking to reduce bugs.

Currently, I can't figure out a better solution. We use the second way to manage model, after all, it is easier to manage.

Here are a few of our types of classification and management:

 - **Request Model / Response Model**. That is, the request parameters and the return model (modified, for frontend view) are placed in the corresponding `.model.ts` directory of the service.
 - **Response Entity**. When using the backend to return results directly, the name has ``entity``, otherwise ``model`` is used.
 - **View Model / Component Model**. When applied to the encapsulation of business components, incoming parameters are passed in by model.

Do you have better practices?

## Question

Is it clean? not yet

### Framework dependent form validation

Since the Angular framework itself provides powerful Reactive Form functionality, we use Reactive Forms in most of the form design, rather than through Entity. This allows us to interact in this part of the UI, relying on the Angular framework, rather than implementing it ourselves.

If you use an Entity mode such as DDD, or use a validator. Later, we also need to develop our own form validation mode, similar to this:

```javascript
{
    Validator: RegExp,
errorMessage: string
}
```


And it means a lot of development costs. However, fortunately, we can try to generalize it.

## Next step

 - **Clean form**. As mentioned above.
 - **Code generation**. Although we have already used Angular Schematics in our project to generate template code. But I believe that in the next step we can use tools to generate pages.
 - **Architectural Guardian**. With a hierarchical structure, it is easier to determine the hierarchical relationship.
 - **Try Other frameworks**.


