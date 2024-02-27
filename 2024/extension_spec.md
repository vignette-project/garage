# Extensions

*Note: This is a incomplete specification of the Extensions architecture. This is not final.*

## Table of Contents
- Summary
- Design
    - Store
        - Endpoints
    - Manifest
        - Packs
        - Dependencies
        - Contributions
    - Assets
        - Types
            - Text
            - Images
            - Videos
            - Sounds
            - Models
            - Binary
            - Prefab
        - Browser
            - Importing as a User
            - Importing as an Extension
    - Scripting
        - Behaviors
            - Attributes
                - Boolean
                - Number
                - String
                - Enum
                - Flag
                - Color
                - Vector4
                - Vector3
                - Vector2
                - Asset
                - Actor
        - Commands
        - Configuration
        - Environment
        - Extensions
        - Locale
        - Sessions
        - Stores
        - Workspace
            - Assets
            - Scenes
            - Views

## Summary
Extensions are packages that is used by the editor to provides additional functions extending the editor's capabilities. 

## Design
### Store
The extensions store provides an interface for consumers to retrieve available extensions. It may be hosted on any backend preferred by its implementers as long as it adheres to the expected endpoints and response messages.

#### Endpoints

### Manifest
Extensions are packaged as `.asar` archives with additional magic in the header.

The additional magic is an extension manifest. An extension manifest is a JSON document that describes an extension. The document structure is as follows:

|Key|Type|Required|Description|
|---|----|:------:|---------------|
|`id`|String|✅|A unique identifier for the extension.|
|`version`|String|✅|A [semver](https://semver.org/)-formatted string.|
|`name`|String||A displayable name for the extension.|
|`description`|String||A displayable description for the extension.|
|`dependencies`|Object[]||An array of dependencies the extension requires.|
|`contributes`|Object||An object of contributions the extension provides used to provide additional content such as but not limited to assets.|

#### Dependencies
An extension may depend on other extensions such as providing additional API. This is declared as an array of objects in the manifest which have the following structure:

|Key|Type|Required|Description|
|---|----|:------:|---------------|
|`id`|String|✅|The unique identifier of an extension.|
|`version`|String|✅|A [semver](https://semver.org/)-formatted string that allows npm-style ranges.|
|`optional`|boolean||Whether this dependency is optional or not to allow the extension to still load even if it is not found.|

#### Contributions
Contributions are a set of JSON declarations that is declared in the extension manifest that extend the functionality of the editor.

- `assets`
- `commands`
- `gizmos`
- `locale`
- `themes`
- `sessions` 
- `script`
- `stores`
- `views`

### Assets
Assets are files contained in the asset browser. These are shared between participants and can only be read from and not written to.

#### Types
Only a subset of MIMEs are accepted as assets. Assets with specified MIMEs can be previewed in the editor.

|Type|MIME type|
|----|---------|
|Text|text/plain<br/>text/json<br/>text/x-markdown|
|Images|image/png<br/>image/jpeg<br/>image/gif<br/>image/svg+xml|
|Videos|video/webm<br/>video/mp4|
|Sounds|audio/webm<br/>audio/mp4<br/>audio/mp3<br/>audio/wav<br/>audio/ogg|
|Models|model/gltf+binary<br/>model/gltf+json<br/>model/mtl<br/>model/obj|
|Prefab|application/x-prefab+json|
|Binary|application/octet-stream|

##### Text
These are plain text files. Supports previewing for plain text, JSON, and markdown files.

##### Images
These are image files. Supported file types are WebP, PNG, JPEG, GIF and SVG.

##### Videos
These are video files. Supported file types are WebM and MP4.

##### Sounds
These are audio files. Supported file types are WebM (audio), MP4 (audio), MP3, WAV, and OGG.

##### Models
These are model files. Supported file types are GLTF (binary), GLTF (JSON), MTL and OBJ.

##### Prefabs
These are prefab files. A prefab is a type of asset that is composed of one or more components and behaviors that can be placed in the editor. Prefabs are JSON files declared by an extension author.

The JSON document structure of a prefab is as follows:

|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`id`|String|✅|The unique identifier of the prefab.|
|`components`|String[]|✅|The components used by the prefab as an array of component or behavior identifiers.|

##### Binary
These are binary files. The asset browser defaults to this file type when it cannot infer the file type of an imported asset.

#### Browser
The asset browser is a virtual file system composed of the user's imported assets and extension contributed assets. The editor may only use assets that are present in the asset browser. If an extension or a user wishes to make their asset available to the editor, they must first import it.

##### Importing as a User
Users may import their assets through the asset browser assuming they have sufficient permissions to perform this action.

##### Importing as an Extension
Extension authors may choose to make their assets available to the editor by declaring the `assets` object array in the extension contributions.

Each element of the array is structured as follows:

|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`type`|String|✅|The asset's type.|
|`path`|String|✅|The asset's path relative to the root of the extension.|

### Scripting
The scripting runtime is backed by a Javascript Engine. All scripts can be written in Javascript but it is recommended to write in Typescript for additional type safety. All scripts are treated as [ES2020](https://262.ecma-international.org/11.0/) modules.

The entry point is expected to have two named functions with the following signatures:

```ts
function activate(): any

function deactivate(): void
```

The scripting API can be accessed through the `vignette` object in the global scope of the entry point script.

#### Behaviors
Behaviors provides an interface for developers to interact with prefabs. A behavior can be registered through the `behaviors` namespace of the scripting API.

```ts
namespace vignette {
    namespace behaviors {
        function registerBehavior(id: string, behavior: Behavior): void;
    }
}
```

##### Attributes
A behavior is able to expose attributes to the editor.

###### Boolean
Boolean attributes are presented as a checkbox in the editor.

**Options**
Boolean attributes have no options.

###### Number
Number attributes are presented as a range slider in the editor.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`min`|Number||The minimum value (inclusive).|
|`max`|Number||The maximum value (inclusive).|
|`step`|Number||The slider step value.|

###### String
String attributes are presented as a textbox in the editor.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`placeholder`|String||The placeholder text shown when no text is placed yet.|

###### Enum
Enum attributes are presented as a dropdown in the editor whose items are from a defined array of strings.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`values`|String[]|✅|An array of enum members as strings.|

###### Flag
Flag attributes are presented as a multi-select dropdown in the editor whose items are from a defined array of strings.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`values`|String[]|✅|An array of enum members as strings.|

###### Color
Color attributes are presented as a colored box which pops out a color picker when clicked on. It also has a textbox for configuring the color as hex.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`hasAlpha`|boolean||Whether to allow an alpha value for this color.|

###### Vector4
Vector4 attributes are presented as a group of textboxes labeled according to its components.

**Options**
Vector4 attributes have no options.

###### Vector3
Vector3 attributes are presented as a group of textboxes labeled according to its components.

**Options**
Vector3 attributes have no options.

###### Vector2
Vector2 attributes are presented as a group of textboxes labeled according to its components.

**Options**
Vector2 attributes have no options.

###### Asset
Asset attributes are presented as readonly textboxes where users can select assets from the asset explorer.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`type`|String||The asset type expected.|

###### Actor
Actor attributes are presented as readonly textboxes where users can select actors from the scene tree.

**Options**
|Key|Type|Required|Description|
|---|----|:------:|-----------|
|`type`|String||The prefab type expected.|

#### Commands
Commands are functions with a unique identifiers and can be invoked from other extensions or from the editor. Commands can be registered through the `commands` namespace of the scripting API.

```ts
namespace vignette {
    namespace commands {
        function executeCommand<T>(id: string, ...args: any[]): T;

        function registerCommand(id: string, command: Command): void;
    }
}
```

#### Configuration
Configuration provides an interface for developers to register handlers for configuration changes. The configuration items are declared from the manifest `contributes` object. It can be accessed through the `configuration` namespace of the scripting API.

```ts
namespace vignette {
    namespace configuration {
        function get<T>(id: string): T;

        function registerChangeHandler<T>(id: string, handler: ConfigurationChangeHandler): void;
    }
}
```

#### Environment
Environment provides an interface for developers to obtain about the runtime environment it is present within. It can be accessed through the `environment` namespace of the scripting API.

```ts
namespace vignette {
    namespace environment {

    }
}
```

#### Extensions
Extensions provides an interface for developers to deal with installed extensions. It can be accessed through the `extensions` namespace of the scripting API.

```ts
namespace vignette {
    namespace extensions {
        function all(): string[];

        function isActive(id: string): boolean;

        function isInstalled(id: string): boolean;

        function getExported<T>(id: string): T;

        function getManifest(id: string | undefined): ExtensionManifest;
    }
}
```

#### Locale
Locale provides an interface for developers to interact with localization-related functionality in the extension system and can be accessed through the `locale` namespace of the scripting API.

```ts
namespace vignette {
    namespace locale {
        function localize(id: string): string;
    }
}
```

#### Sessions
Sessions provides an interface for developers to interact with the current session and register their own session handlers. A session handler can be registered through the `sessions` namespace of the scripting API.

```ts
namespace vignette {
    namespace sessions {
        function registerSessionHandler(id: string, handler: SessionHandler): void;
    }
}
```

#### Stores
Stores provide an interface for developers to register their own extension stores. An extension store can be registered through the `stores` namespace of the scripting API.

```ts
namespace vignette {
    namespace stores {
        function registerStore(id: string, store: ExtensionStore): void;
    }
}
```

#### Workspace
The `workspace` namespace contains the workspace-related functionality of the editor.

##### Assets
The `assets` namespace contains the asset browser-related functionality of the editor.

```ts
namespace vignette {
    namespace workspace {
        namespace assets {
            function stat(path: string): FileStat;

            function readFile(path: string): Uint8Array;

            function readDirectory(path: string): string[];
        }
    }
}
```

##### Scenes
The `scene` namespace contains scene-related functionality of the editor.

```ts
namespace vignette {
    namespace workspace {
        namespace scene {
            function registerGizmo(id: string, gizmo: Gizmo): void;
        }
    }
}
```

##### Views
Views provide an interface for developers to register their own views.

```ts
namespace vignette {
    namespace workspace {
        namespace views {

        }
    }
}
```
