# Component-based Architecture's patterns

There's a lot of holes (issues) when implement Component-based Architecture, so I wrote a articles to share it.

## Pattens on Component-based Architecture

In daily development, there are some common ways to use components:

1. Select a base-component library. such as Material Design, Bootstrap UI.
2. When we need to add functionality to the base-component, we need to facade/encapsulation/decorator our own components.
3. When we can't find the right component, we need to write the component ourselves.
4. Some components that do not exist in the base-component library, but there are third-party component libraries that require to facade/encapsulation/decorator it for our project.
5. Components written by myself have bugs, so they are modified to lead to new bugs.
6. Component being written is too bloated and split into two components; the two components are duplicated and merged into one component.
7. Repeat steps 2 ~ 6 repeatedly, or just repeat step 5, or just repeat step 6.

Yes, we are mainly dealing with: unpredictable bugs.

### Encapsulation components: Decorator mode

First, we can't take the time to encapsulation all the components twice - unless we intend to package our own component libraries. Then, for those components that we need to modify, just encapsulation it. The rest, we are going to discuss: third-party components.

In most projects, when we select component libraries, we often choose open source, free, and commercially available components. Some basic component libraries often do not contain complex components, such as tables, rich text editors and other components. Or, the behavior of a component does not meet expectations, so it is necessary to find a third-party component.

For these third-party components, there is nothing to say, all secondary packaging:

 - encapsulation input and output on demand
 - modify it to your own styleguide
 - as general as possible, rather than relying on three-party component

The goal is to reduce the risk of changes to the three-party components - they don't seem to have a base component library, they are powerful, have strong organizational support :( . In case there is a problem someday, we can also take care of it ourselves.

In a sense, the encapsulation of a component is also a combination of components, but the scenario it applies to is not the same. In terms of name, secondary encapsulation of third-party components is much more applicable than combining third-party components.

### Split and composition of domain components

The Ccomposition and split of components is still a pain for me. Even if I did a lot of projects and smashed a lot of pits, I didn't think of best practices. So:

 - We feel that the A component and the B component are the same function, but the parameters are different. I wrote the same component, and then we started to vomit.
 - We think that the A component and the B component are two different components, so they become two components. However, after the business has been modified, they may only have differences in the head.

The only thing that can be summed up is:

 - If there are only two or three basic properties, such as styles or displays, then if you composition a component, the problem is not that big.
 - If there are a lot of differences, and there are big differences in performance and behavior, then splitting into two components may be a good choice.

In addition, depending on where you use it, such as the details page and some parts of the list page, they are the same at the beginning of the design, but they may be different sooner or later - but they alost can be the same, hahaha.

Excessive design and inadequate design are pits that we often encounter. Seeing the move, it is probably what we can do. In addition, the most interesting thing is that once we split into two components, we don't want to go back together, hahahaha.

## Composition or Inheritance

The Composition of components, that is, based on the reference subcomponents, adds some new behavior - you may also need to add input and output on the parent component.

The continuation of the component, by adding the parent component, adds some new behavior.

For components, we focus on two parts: input and output. So for components that use Angular, you only need the parent component to expose ``inputs`` and ``outputs``, which makes it easy to inherit input and output.

```typescript
@Component({
 selector: 'app-clean-navbar',
 inputs: [...NavBarMeta.inputs],
 outputs: [...NavBarMeta.outputs],
 templateUrl: './clean-navbar.component.html',
 styleUrls: ['./clean-navbar.component.scss']
})
```

In addition, since most Angular projects use TypeScript. Therefore, parameters such as inputs and outptus can also be annotated to get the corresponding values.

Given the painful experience of maintaining a 10-year legacy system in the past, I am not so keen on inheritance - when we use too much inheritance, we encounter a natural pit: parent class layer debugging. However, if there is only one or two layers of inheritance, then there is not much problem.

So, an easy way is to use the combination - in the required part, plus custom properties.

```javascript
function WelcomeDialog() {
 return (
 <Dialog
   title="Welcome"
   message="Thank you for visiting our spacecraft!" />
 );
}
```

At Facebook, we use React in thousands of components, and we haven’t found any use cases where we would recommend creating component inheritance hierarchies.


## Conclusion

> No silver bullets.





