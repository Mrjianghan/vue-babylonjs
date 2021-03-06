### Entity

The basic Entity component to manipulate and transform the scene with

#### Details

 - Use Entity as if it were an HTML `div` to manipulate and group child components

#### Usage

```html
<Scene>
  <Entity v-model="myEntity" :position="[0, 0, 5]">
    <Animation propety="rotation.y" :duration="5" :end="Math.PI * 2"></Animation>
    <!-- this box will be rotated relative to the parent entity during the animation -->
    <Box></Box>
  </Entity>
</Scene>
```

#### Props

 - `name` (String) - The unique name to give an entity in BabylonJS (automatic random uid if not defined)
 - `position` (Vector3) - The position vector in 3D space relative to the parent
 - `rotation` (Vector3) - The rotation vector (in radians) relative to the parent
 - `scaling` (Vector3) - The scaling vector that will stretch/compress this entity and its children
 - `properties` (Object) - Other properties to set on the underlying BabylonJS object if not available as a prop (attribute)

##### Mixin functionality:

 - The Entity component can be used as a Vue mixin, import/require `vue-babylonjs/entity` to use it in this manner
 - The default Entity mixin includes transform properties like `position`, `rotation`, and `scaling`, use `vue-babylonjs/lib/entity/abstract` instead to omit this functionality
 - When using the abstract entity instead for a mixin, be sure to call `this.$replace(entity)` to set the Entity's underlying BabylonJS object
 - In the source code, methods and variables with a leading underscore (`_`) are marked "private" and are not guaranteed to remain or function the same in future versions of the plugin
 - Most components, if not all indirectly, inherit from Entity through this mixin functionality
 - To better understand this functionality it can be helpful to read the source code of the other components defined in this plugin
 - Most other components in this library can also be used as a mixin, but their property and function names may conflict with your names, when in doubt check the source code of the component you are mixing in
 - Look through the directory tree in `lib` to see what components are available to mixin

Public properties:

 - `$entity` - A reference to the underlying BabylonJS object for this Entity
 - `$scene` - A reference to the root Scene BabylonJS object
 - `$bus` - A Vue event bus to pass events to the parent Entity
 - `$event` - A Vue event bus to pass events to the children of this component (becomes that child's `$bus`)

Public methods:

 - `$replace` - set the value of this Entity's `$entity` (the underlying BabylonJS object in most cases)

Custom Lifecycle Hooks:

 - Lifecycle hooks are functions bound to a `this` context of the Entity component
 - These functions can be `async` or return a Promise to resolve asynchronously
 - The following are public lifecycle hook functions:
    - `beforeScene` - called before the Scene is finished initializing or immediately when the Scene exists
    - `onScene` - called immediately after the Scene is initialized
    - `onParent` - called after the parent Entity is resolved
    - `onEntity` - called after this Entity's underlying `$entity` is resolved
    - `beforeRender` - called before every frame renders
    - `afterRender` - called after every frame renders
 - These functions are passed a single object containing references to variables used at that stage of the Entity lifecycle, the object will have the following properties:
    - `classes` - a reference to all of BabylonJS (always available)
    - `name` - a reference to this Entity's unique `name` value (always available)
    - `scene` - a reference to the root Scene object from BabylonJS (available during and after `onScene`)
    - `parent` - a reference to the parent Entity's `$entity` (available during and after `onParent`)
    - `entity` - a reference to the underlying `$entity` object (available during and after `onEntity`)
 - **Warning** lifecycle hook functions do not merge when mixed in like native Vue lifecycle events

Custom Event Hooks:

 - Optionally if a `events` object is defined in the component then functions defined within will be called on events with names matching the properties of this object
 - This is for events fired on the `$event` bus from the children of this Entity
 - Functions defined within will be passed a single object conatining the value passed from the event
 - **Warning** the properties defined within `events` will not be merged if used as a mixin

### Property

Change a value in an Entity's underlying BabylonJS object

#### Details

 - This system was developed to allow for manipulation to properties outside the scope of this Plugin
 - This plugin will not specify every possible property for every class in BabylonJS, use this Property system or obtain a reference to the underlying BabylonJS object in your Vue component script and consult the BabylonJS API documentation
 - These values are reactive, but check the BabylonJS class for the object you are manipulating to be sure this will actually change in the Scene
 - Use only one of the `any`, `float`, `color`, `vector`, or `matrix` props (attributes)

#### Usage

```html
<Scene>
  <Property name="someSceneProperty" :any="{ aProperty: 'a value' }"></Property>
</Scene>
```

#### Props

 - `name` (String) - the name of the property to set
 - `any` (any) - a value of any type to set
 - `float` (Number) - a number value to set
 - `color` (Color) - a Color from a helper or BabylonJS to set
 - `vector` (Vector) - a Vector from a helper or BabylonJS to set
 - `matrix` (Matrix) - a Matrix from a helper or BabylonJS to set
