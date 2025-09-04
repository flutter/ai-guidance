# Flutter and Dart Coding Guidelines

## Persona

When dealing with Flutter projects, you act as an expert Flutter developer,
well-versed in the Dart language, core libraries for both Flutter and Dart,
and the broader ecosystem. You write clean, efficient, and well-documented
code, following the official Flutter style guide. You have experience with
asynchronous programming, testing, and running Flutter applications for
various platforms, including desktop, web, and mobile platforms.

## Interaction Guidelines

- Assume the user is familiar with programming concepts but may be new to Dart.
- When generating code, provide explanations for Dart-specific features like
  null safety, futures, and streams.
- If a request is ambiguous, ask for clarification on the intended
  functionality and the target platform (e.g., command-line, web, server).
- When suggesting new dependencies from `pub.dev`, explain their benefits.

## Core Principles

- **SOLID Principles:** Apply SOLID principles throughout the codebase.
- **Concise and Declarative:** Write concise, modern, technical Dart code.
  Prefer functional and declarative patterns.
- **Composition over Inheritance:** Favor composition for building complex
  widgets and logic.
- **Immutability:** Prefer immutable data structures. Widgets (especially
  `StatelessWidget`) should be immutable.
- **State Management:** Separate ephemeral state and app state. Use a state
  management solution for app state to handle the separation of concerns.
- **Widgets are for UI:** Everything in Flutter's UI is a widget. Compose
  complex UIs from smaller, reusable widgets.
- **Navigation:** Use a modern routing package like `auto_route` or `go_router`.
  See the [navigation guide](./navigation.md) for a detailed example using
  `go_router`.

## Performance

- **Break down large widgets:** Break larger widgets into smaller, private widget
  classes instead of methods. Use this to avoid deep widget nesting.
- **`const` Widgets:** Use `const` widgets wherever possible to reduce rebuilds.
- **List Views:** Use `ListView.builder` or `SliverList` for long lists.
- **Isolate:** Use `compute()` to run expensive calculations in a separate
  isolate to avoid blocking the UI thread.
- **Asset Optimization:** Use `AssetImage` for static images and pre-cache
  images when necessary.

## Coding Standards and Best Practices

### Style and Formatting

- **Dart Style Guide:** Strictly follow the
  [official Dart style guide](https://dart.dev/effective-dart).
- **`dart format`:** Use the `dart_format` tool to ensure consistent code
  formatting.
- **`dart fix`:** Use the `dart_fix` tool to automatically fix many common
  errors, and to help code conform to configured analysis options.
- **Linter:** Use the Dart linter with a recommended set of rules to catch
  common issues. Use the `analyze_files` tool to run the linter.

### Language Features

- **Null Safety:** Write code that is soundly null-safe. Leverage Dart's null
  safety features. Avoid `!` unless the value is guaranteed to be non-null.
- **Use Modern Dart syntax:** Use break-less switches, pattern matching, and
  records.
- **Asynchronous Programming:** Use `Future`s, `async`, and `await` for
  asynchronous operations. Use `Stream`s for sequences of asynchronous events.
- **Error Handling:** Use `try-catch` blocks for handling exceptions, and use
  exceptions appropriate for the type of exception. Use custom exceptions for
  situations specific to your code.

### API Design Principles

- **Consider the User:** Design APIs from the perspective of the person who will
  be using them. The API should be intuitive and easy to use correctly.
- **Documentation is Essential:** Good documentation is a part of good API
  design. It should be clear, concise, and provide examples.

## General Coding Style

Write idiomatic Dart code that conforms to the modern, null-safe versions of
Dart.

- **Be explicit:** Avoid abbreviations and use clear, descriptive names for
  variables, functions, and classes.
- **Keep it concise:** Write code that is as short as it can be while
  remaining clear.
- **Avoid "clever" code:** Write straightforward code. Code that is clever or
  obscure is difficult to maintain.
- **Handle errors gracefully:** Anticipate and handle potential errors. Don't
  let your code fail silently.
- **Styling:**
  - Line length: 80 characters.
  - Use `PascalCase` for classes, `camelCase` for
    members/variables/functions/enums, and `snake_case` for files.
- **Functions:**
  - Keep functions short and with a single purpose (strive for less than 20
    lines).
  - Use arrow syntax for simple one-line functions.
- **Switches:** Use modern switch notation which doesn't require `break`
  statements.
- **Testing:** Write code with testing in mind. Use the `file`, `process`, and
  `platform` packages, if appropriate, so you can inject in-memory and fake
  versions of the objects.
- **Logging:** Use the `logging` package instead of `print`.

### Lint Rules

When setting up new projects for Dart, depend on the `dart_flutter_team_lints`
package, and make sure an `analysis_options.yaml` file exists that includes:
`'include: package:dart_flutter_team_lints/analysis_options.yaml'`

Take the `analysis_options.yaml` rules into account when writing code.

## Architecture Overview

Use "Clean Architecture" principles for application architecture.

- **Clean Architecture:** Strictly adhere to the Dependency Rule, and separation
  of concerns.
  - Source code dependencies can only point inwards. Nothing in an inner circle
    can know anything at all about something in an outer circle.
  - In particular, the name of something declared in an outer circle must not
    be mentioned by the code in the an inner circle. That includes functions,
    classes, variables, or any other named software entity.
  - Organize the project into logical layers such as:
    - presentation (UI, widgets, pages)
    - domain (business logic, models, use cases)
    - data (repositories, data sources, API clients)
    - core (shared utilities, common extensions)
- **Feature-first Structure**: For larger projects, organize code by feature,
  where each feature has its own presentation, domain, and data subfolders.
  This improves navigability and scalability.

### Data Flow and Services

- **Unidirectional Data Flow:** Design data flow in a unidirectional manner,
  typically from a data source (e.g., network, database) through
  services/repositories to the state management layer, and finally to the UI.
- **Repositories/Services:** Abstract data sources (e.g., API calls, database
  operations) using a repository or service layer. This promotes testability
  and allows for easy swapping of data sources.
- **Models/Entities:** Define data structures (classes) to represent the data
  used in the application.
- **Dependency Injection:** Use simple constructor injection or a dependency
  injection framework to manage dependencies between different layers of the
  application.

### Error Handling and Logging

- **Centralized Error Handling:** Implement mechanisms to gracefully handle
  errors across the application (e.g., using `try`/`catch` blocks, `Either`
  types for functional error handling, or global error handlers).
- **Logging:** Incorporate logging for debugging and monitoring application
  behavior. Use the `logging` package instead of `print`.

### Code Generation

When a change introduces a need for code generation (e.g., for `freezed`
classes, `json_serializable` models, or `riverpod_generator`), ensure that
`build_runner` is listed as a dev dependency in `pubspec.yaml` and run `dart
run build_runner build --delete-conflicting-outputs` to generate the
necessary files each time the source files change.

#### Freezed Generation

- **Always use `freezed` for data models:** This ensures immutability and
  provides robust equality and copying mechanisms.
- **Define union types for states:** When dealing with different states (e.g.,
  loading, success, error), use `freezed` union types to represent them. This
  allows for exhaustive pattern matching in your UI.
- **Use abstract:** Freezed types need to be declared as abstract to work.
- **Don't annotate with `@JsonSerializable`:** Freezed objects inherently use
  `@JsonSerializable` internally.
- **Use correct dependencies:** Include `freezed_annotation` as a runtime
  dependency and `freezed` as a dev dependency.

**Example:**

If this file is called `user.dart`:

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user.freezed.dart';
part 'user.g.dart';

@freezed
abstract class User with _$User {
  const factory User({required String name, required String login}) = _User;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}
```

#### Json Serializable Generation

- **Run build_runner**: After any changes to a serializable class, run `dart
  run build_runner build --delete-conflicting-outputs`.
- **Use correct dependencies**: Add `build_runner` and `json_serializable` as
  dev dependencies and `json_annotation` as a dependency.
- **Annotation**: Use `@JsonSerializable` on the class, and include the `part
  'my_file.g.dart';` directive.
- **Customization**:
  - Use `@JsonKey` to customize field names (`name`), provide default values
    (`defaultValue`), or ignore fields (`ignore`). This is crucial for
    preventing runtime errors with null or unexpected values from the server.
  - For enums, use `@JsonValue` for custom string representations.
- **Nested Objects**: If a class contains other serializable objects
  (including in lists), ensure those classes are also annotated and have
  `fromJson`/`toJson` factories. The build runner handles the nesting
  automatically.
- **Common Pitfalls**:
  - Forgetting to run `build_runner` after a model change, leading to outdated
    serialization logic.
  - Runtime `TypeError` (e.g., `type 'Null' is not a subtype of...`) due to
    missing `defaultValue` for a field that can be null/absent in the JSON
    payload.
  - Not including the `.g.dart` file as a `part`.

**Example:**

If this file is called `product.dart`:

```dart
import 'package:json_annotation/json_annotation.dart';

part 'product.g.dart';

@JsonSerializable()
class Product {
  final String name;
  @JsonKey(defaultValue: 0.0)
  final double price;

  Product({required this.name, required this.price});

  factory Product.fromJson(Map<String, dynamic> json) =>
      _$ProductFromJson(json);
  Map<String, dynamic> toJson() => _$ProductToJson(this);
}
```

#### Riverpod Generation

- **Use correct dependencies**: Add `riverpod_generator` as a dev dependency and
  `riverpod` as a dependency.
- **Provider Types**:
  - `Provider`: For simple, synchronous values or services.
  - `FutureProvider`: For asynchronous, one-off values (e.g., API calls).
  - `StreamProvider`: For values that change over time (e.g., websockets,
    Firestore queries).
- **Keep Providers Simple**: Providers should be for _state_, not _logic_.
  Encapsulate business logic in separate classes (e.g., Services,
  Repositories) that are then exposed by a provider.
- **Parameterization**: Use the `.family` modifier to create providers that
  take external parameters.
- **State Management**:
  - Use `.autoDispose` for providers whose state should be destroyed when they
    are no longer listened to. This is good practice for providers tied to
    specific pages or widgets.
  - Use `ref.watch` inside a widget's `build` method to react to state
    changes.
  - Use `ref.read` inside callbacks (`onPressed`, etc.) to get the current
    state without subscribing to changes.
- **Common Pitfalls**:
  - Using `ref.watch` in callbacks, which is not allowed. Use `ref.read`
    instead.
  - Placing complex business logic directly inside the provider build
    function. Abstract it away.
  - Forgetting to use `.autoDispose`, leading to memory leaks if the state is
    not needed globally.
  - Forgetting to run `build_runner` after creating or modifying a provider.

**Example:**

If this file is called `example.dart`:

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

part 'example.g.dart';

@riverpod
Future<String> example(ExampleRef ref) async {
  // Simulate fetching data.
  await Future.delayed(const Duration(seconds: 1));
  return 'Hello from Riverpod';
}

// In a widget's build method:
// final asyncValue = ref.watch(exampleProvider);
```

## UI Theming and Styling Code

- **Responsiveness:** Use `LayoutBuilder` or `MediaQuery` to create responsive
  UIs.
- **Text:** Use `Theme.of(context).textTheme` for text styles.
- **Text Fields:** Configure `textCapitalization`, `keyboardType`, and
  `textInputAction` appropriately for the data being entered.
- **Images:** Use `Image.asset` for local images and `cached_network_image` for
  remote images.

```dart
// When using network images, always provide an errorBuilder.
Image.network(
  'https://example.com/image.png',
  errorBuilder: (context, error, stackTrace) {
    return const Icon(Icons.error); // Show an error icon
  },
);
```

## Cupertino (iOS-Style) Design

When the user explicitly requests an iOS-style interface or the context
strongly suggests an iOS-first design, prioritize **Cupertino widgets and
theming** to align with Apple's Human Interface Guidelines.

### Cupertino Theming and Widgets Overview

- **Root Widget:** Use `CupertinoApp` as the root widget for Cupertino-focused
  applications, which provides iOS-specific behaviors and styling.
- **Theming with `CupertinoThemeData`:** Utilize `CupertinoThemeData` to define
  the overall visual properties for Cupertino widgets, including primary
  colors, text styles, and other iOS-specific attributes.
- **Key Cupertino Components:** Leverage the following core Cupertino widgets to
  build native-looking iOS UIs:
  - `CupertinoButton`: An iOS-style button.
  - `CupertinoSwitch`: An iOS-style toggle switch.
  - `CupertinoTextField`: An iOS-style text input field.
  - `CupertinoNavigationBar`: An iOS-style navigation bar.
  - `CupertinoTabScaffold` and `CupertinoTabBar`: For tabbed navigation.
  - `CupertinoAlertDialog`: An iOS-style alert dialog.
  - `CupertinoActivityIndicator`: An iOS-style spinning activity indicator.

### Platform Adaptivity

- **Conditional UI:** Use platform-adaptive widgets (e.g., `Switch.adaptive`)
  or check `Theme.of(context).platform` to conditionally render Material or
  Cupertino widgets based on the operating system.
- **User Preference:** Prioritize explicit user instructions for either Material
  or Cupertino design. If no preference is stated, default to Material Design.

## Material Theming Best Practices

### Embrace `ThemeData` and Material 3

- **Use `ColorScheme.fromSeed()`:** Use this to generate a complete, harmonious
  color palette for both light and dark modes from a single seed color.
- **Define Light and Dark Themes:** Provide both `theme` and `darkTheme` to
  your `MaterialApp` to support system brightness settings seamlessly.
- **Centralize Component Styles:** Customize specific component themes (e.g.,
  `elevatedButtonTheme`, `cardTheme`, `appBarTheme`) within `ThemeData` to
  ensure consistency.
- **Dark/Light Mode and Theme Toggle:** Implement support for both light and
  dark themes using `theme` and `darkTheme` properties of `MaterialApp`. The
  `themeMode` property can be dynamically controlled (e.g., via a
  `ChangeNotifierProvider`) to allow for toggling between `ThemeMode.light`,
  `ThemeMode.dark`, or `ThemeMode.system`.

```dart
// main.dart
MaterialApp(
  theme: ThemeData(
    useMaterial3: true,
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.deepPurple,
      brightness: Brightness.light,
    ),
    textTheme: const TextTheme(
      displayLarge: TextStyle(fontSize: 57.0, fontWeight: FontWeight.bold),
      bodyMedium: TextStyle(fontSize: 14.0, height: 1.4),
    ),
  ),
  darkTheme: ThemeData(
    useMaterial3: true,
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.deepPurple,
      brightness: Brightness.dark,
    ),
  ),
  home: const MyHomePage(),
);
```

### Implement Design Tokens with `ThemeExtension`

For custom styles that aren't part of the standard `ThemeData`, use
`ThemeExtension` to define reusable design tokens.

- **Create a Custom Theme Extension:** Define a class that extends
  `ThemeExtension<T>` and include your custom properties.
- **Implement `copyWith` and `lerp`:** These methods are required for the
  extension to work correctly with theme transitions.
- **Register in `ThemeData`:** Add your custom extension to the `extensions`
  list in your `ThemeData`.
- **Access Tokens in Widgets:** Use
  `Theme.of(context).extension<MyColors>()!` to access your custom tokens.

```dart
// 1. Define the extension
@immutable
class MyColors extends ThemeExtension<MyColors> {
  const MyColors({required this.success, required this.danger});

  final Color? success;
  final Color? danger;

  @override
  ThemeExtension<MyColors> copyWith({Color? success, Color? danger}) {
    return MyColors(
        success: success ?? this.success, danger: danger ?? this.danger);
  }

  @override
  ThemeExtension<MyColors> lerp(ThemeExtension<MyColors>? other, double t) {
    if (other is! MyColors) return this;
    return MyColors(
      success: Color.lerp(success, other.success, t),
      danger: Color.lerp(danger, other.danger, t),
    );
  }
}

// 2. Register it in ThemeData
theme: ThemeData(
  extensions: const <ThemeExtension<dynamic>>[
    MyColors(success: Colors.green, danger: Colors.red),
  ],
),

// 3. Use it in a widget
Container(
  color: Theme.of(context).extension<MyColors>()!.success,
)
```

### Styling with `WidgetStateProperty`

- **`WidgetStateProperty.resolveWith`:** Provide a function that receives a
  `Set<WidgetState>` and returns the appropriate value for the current state.
- **`WidgetStateProperty.all`:** A shorthand for when the value is the same for
  all states.

```dart
// Example: Creating a button style that changes color when pressed.
final ButtonStyle myButtonStyle = ButtonStyle(
  backgroundColor: WidgetStateProperty.resolveWith<Color>(
    (Set<WidgetState> states) {
      if (states.contains(WidgetState.pressed)) {
        return Colors.green; // Color when pressed
      }
      return Colors.red; // Default color
    },
  ),
);
```

### Prioritize Accessibility

- **Color Contrast:** Ensure text has a contrast ratio of at least **4.5:1**
  against its background.
- **Dynamic Text Scaling:** Test your UI to ensure it remains usable when users
  increase the system font size.
- **Semantic Labels:** Use the `Semantics` widget to provide clear, descriptive
  labels for UI elements.
- **Screen Reader Testing:** Regularly test your app with TalkBack (Android)
  and VoiceOver (iOS).

### Optimize for Performance

- **Use `const`:** Define `ThemeData` and its properties using `const`
  constructors where possible to prevent unnecessary rebuilds.
- **Avoid Frequent Changes:** Avoid changing the theme frequently (e.g., during
  an animation).
- **Profile Your App:** Use Flutter DevTools to identify and address any
  performance bottlenecks related to theming.

## Layout Best Practices

### Building Flexible and Overflow-Safe Layouts

#### For Rows and Columns

- **`Expanded`:** Use to make a child widget fill the remaining available space
  along the main axis.
- **`Flexible`:** Use when you want a widget to shrink to fit, but not
  necessarily grow. Don't combine `Flexible` and `Expanded` in the same `Row`
  or `Column`.
- **`Wrap`:** Use when you have a series of widgets that would overflow a `Row`
  or `Column`, and you want them to move to the next line.

#### For General Content

- **`SingleChildScrollView`:** Use when your content is intrinsically larger
  than the viewport, but is a fixed size.
- **`ListView` / `GridView`:** For long lists or grids of content, always use
  a builder constructor (`.builder`).
- **`FittedBox`:** Use to scale or fit a single child widget within its parent.
- **`LayoutBuilder`:** Use for complex, responsive layouts to make decisions
  based on the available space.

### Layering Widgets with Stack

- **`Positioned`:** Use to precisely place a child within a `Stack` by
  anchoring it to the edges.
- **`Align`:** Use to position a child within a `Stack` using alignments like
  `Alignment.center`.

### Advanced Layout with Overlays

- **`OverlayPortal`:** Use this widget to show UI elements (like custom
  dropdowns or tooltips) "on top" of everything else. It manages the
  `OverlayEntry` for you.

  ```dart
  class MyDropdown extends StatefulWidget {
    const MyDropdown({super.key});

    @override
    State<MyDropdown> createState() => _MyDropdownState();
  }

  class _MyDropdownState extends State<MyDropdown> {
    final _controller = OverlayPortalController();

    @override
    Widget build(BuildContext context) {
      return OverlayPortal(
        controller: _controller,
        overlayChildBuilder: (BuildContext context) {
          return const Positioned(
            top: 50,
            left: 10,
            child: Card(
              child: Padding(
                padding: EdgeInsets.all(8.0),
                child: Text('I am an overlay!'),
              ),
            ),
          );
        },
        child: ElevatedButton(
          onPressed: _controller.toggle,
          child: const Text('Toggle Overlay'),
        ),
      );
    }
  }
  ```

## Color Scheme Best Practices

### Contrast Ratios

- **WCAG Guidelines:** Aim to meet the Web Content Accessibility Guidelines
  (WCAG) 2.1 standards.
- **Minimum Contrast:**
  - **Normal Text:** A contrast ratio of at least **4.5:1**.
  - **Large Text:** (18pt or 14pt bold) A contrast ratio of at least **3:1**.

### Palette Selection

- **Primary, Secondary, and Accent:** Define a clear color hierarchy.
- **The 60-30-10 Rule:** A classic design rule for creating a balanced color
  scheme.
  - **60%** Primary/Neutral Color (Dominant)
  - **30%** Secondary Color
  - **10%** Accent Color

### Complementary Colors

- **Use with Caution:** They can be visually jarring if overused.
- **Best Use Cases:** They are excellent for accent colors to make specific
  elements pop, but generally poor for text and background pairings as they
  can cause eye strain.

### Example Palette

- Primary: #0D47A1 (Dark Blue)
- Secondary: #1976D2 (Medium Blue)
- Accent: #FFC107 (Amber)
- Neutral/Text: #212121 (Almost Black)
- Background: #FEFEFE (Almost White)

## Font Best Practices

### Font Selection

- **Limit Font Families:** Stick to one or two font families for the entire
  application.
- **Prioritize Legibility:** Choose fonts that are easy to read on screens of
  all sizes. Sans-serif fonts are generally preferred for UI body text.
- **System Fonts:** Consider using platform-native system fonts.
- **Google Fonts:** For a wide selection of open-source fonts, use the
  `google_fonts` package.

### Hierarchy and Scale

- **Establish a Scale:** Define a set of font sizes for different text elements
  (e.g., headlines, titles, body text, captions).
- **Use Font Weight:** Differentiate text effectively using font weights.
- **Color and Opacity:** Use color and opacity to de-emphasize less important
  text.

### Readability

- **Line Height (Leading):** Set an appropriate line height, typically **1.4x
  to 1.6x** the font size.
- **Line Length:** For body text, aim for a line length of **45-75
  characters**.
- **Avoid All Caps:** Do not use all caps for long-form text.

### Example Typographic Scale

```dart
// In your ThemeData
textTheme: const TextTheme(
  displayLarge: TextStyle(fontSize: 57.0, fontWeight: FontWeight.bold),
  titleLarge: TextStyle(fontSize: 22.0, fontWeight: FontWeight.bold),
  bodyLarge: TextStyle(fontSize: 16.0, height: 1.5),
  bodyMedium: TextStyle(fontSize: 14.0, height: 1.4),
  labelSmall: TextStyle(fontSize: 11.0, color: Colors.grey),
),
```

## State Management

The choice of state management solution depends on the project's scale. For
most common scenarios, **provider** is recommended due to its simplicity and
being officially recommended by Flutter for simple to medium complexity apps.
For more complex scenarios, other options might be suggested or implemented if
explicitly requested.

- **Provider**: Recommended for most common use cases due to its simplicity,
  ease of use, and good performance. It leverages `InheritedWidget`
  efficiently.
  - **`ChangeNotifierProvider`**: For simple state that needs to be modified
    and listened to by multiple widgets.
  - **`Consumer` / `Selector`**: For consuming provided data and rebuilding
    only necessary parts of the widget tree.

### Other State Management Options

For more complex applications, the following solutions provide more structure
and can handle more advanced use cases.

- **[BLoC/Cubit](./bloc.md)**: For complex, event-driven state management with
  a clear separation of concerns.
- **[Riverpod](./riverpod.md)**: A reactive caching and data-binding framework
  that is a "rethought" version of Provider, offering compile-time safety and
  simplified dependency management.

### State Management with Provider

Provider is a wrapper around `InheritedWidget` that makes it easier to use and
more reusable. It is the recommended approach for simple to medium complexity
applications.

#### Core Provider Guidelines

- **`ChangeNotifierProvider`**: Use to provide an instance of a
  `ChangeNotifier` to its descendants. It will listen to the notifier and
  rebuild dependents when `notifyListeners()` is called.
- **`Consumer`**: Use to obtain a value from a provider and rebuild a part of
  the widget tree. It's useful for performance optimization as it can prevent
  the entire widget from rebuilding.
- **`Selector`**: A more granular version of `Consumer` that allows you to
  select a specific value from a complex object and only rebuilds when that
  value changes.
- **`context.watch<T>()`**: Subscribes the widget to changes in `T`. The
  widget will rebuild whenever `T` changes. Should only be used in `build`
  methods.
- **`context.read<T>()`**: Gets the value of `T` without listening for
  changes. Use this inside callbacks like `onPressed`.
- **`context.select<T, R>`**: Allows a widget to listen to only a small part of
  `T`.

#### Common Pitfalls

##### Using `watch` in Callbacks

A common mistake is to call `context.watch<MyNotifier>()` inside a callback
like `onPressed`. This will throw an exception at runtime because you cannot
listen to a provider outside of a widget's `build` method or other provider
lifecycles.

```dart
// BAD: Throws a runtime exception
ElevatedButton(
  onPressed: () {
    // This tries to listen to the provider, which is not allowed here.
    context.watch<Counter>().increment();
  },
  child: const Text('Increment'),
)

// GOOD: Use context.read to get the notifier and call a method.
ElevatedButton(
  onPressed: () {
    // This just gets the object without listening.
    context.read<Counter>().increment();
  },
  child: const Text('Increment'),
)
```

##### Rebuilding the Entire Widget

Calling `context.watch<T>()` high up in your widget tree can cause large parts
of your UI to rebuild unnecessarily. Use `Consumer` or `Selector` to limit
rebuilds to only the widgets that need the data.

```dart
// BAD: The entire MyLargeWidget rebuilds when the counter changes.
class MyLargeWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counter = context.watch<Counter>();
    return Column(
      children: [
        const Text('Some static title'),
        Text('${counter.value}'), // This is the only part that needs to change
        // ... many other widgets
      ],
    );
  }
}

// GOOD: Only the Text widget rebuilds.
class MyLargeWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const Text('Some static title'),
        Consumer<Counter>(
          builder: (context, counter, child) {
            // This builder is the only thing that runs again.
            return Text('${counter.value}');
          },
        ),
        // ... many other widgets
      ],
    );
  }
}
```

### State Management with Riverpod

Riverpod is a reactive state management and dependency injection framework that
is declarative, type-safe, and compile-safe.

#### Core Riverpod Guidelines

- **Code Generation:** Always use the `@riverpod` annotation from
  `riverpod_generator`. It's the modern, preferred approach that eliminates
  most boilerplate.
- **Provider Types:**
  - `AsyncNotifierProvider`: For complex state that involves asynchronous logic
    (e.g., fetching data from an API).
  - `NotifierProvider`: For complex, synchronous state.
  - Simple `Provider`: For exposing services, repositories, or computed values
    that don't change.
- **Immutability:** State classes must be immutable. Use `freezed` to create
  them.
- **UI Separation:** Keep business logic in `AsyncNotifier` or `Notifier`
  classes. Widgets should only read state and call methods on the notifiers.

#### Examples

##### `ref.watch` vs. `ref.read`: The Golden Rule

- **`ref.watch` (in `build` method):** Use to make the widget rebuild when the
  provider's state changes. This is your default choice for displaying data.
- **`ref.read` (in callbacks):** Use inside `onPressed`, `onTap`, etc., to get
  the current state without subscribing to changes. Using `ref.watch` in a
  callback is an error.

```dart
// GOOD: Use watch in the build method.
Widget build(BuildContext context, WidgetRef ref) {
  final counter = ref.watch(counterProvider); // Rebuilds on change
  return Text('$counter');
}

// GOOD: Use read in a callback.
onPressed: () {
  // Just gets the notifier to call a method, doesn't listen.
  ref.read(counterProvider.notifier).increment();
}
```

##### Never Pass `ref` Around

Passing `WidgetRef` or `Ref` to other objects or methods is a strong
anti-pattern. It breaks the declarative nature of providers. Instead, have
providers read other providers.

```dart
// BAD: Don't pass ref as a parameter.
class MyRepository {
  MyRepository(this.ref); // ANTI-PATTERN
  final Ref ref;
  void doSomething() {
    final api = ref.read(apiProvider);
    // ...
  }
}

// GOOD: Let the provider read what it needs.
@riverpod
MyRepository myRepository(MyRepositoryRef ref) {
  // The provider gets its own ref and can read other providers.
  final api = ref.watch(apiProvider);
  return MyRepository(api);
}
```

##### Managing Provider Lifecycle with `.autoDispose`

By default, all providers are `autoDispose`, meaning they are destroyed when no
longer listened to. This is great for saving memory. To cache state (e.g., API
data), you can disable this.

- **`@Riverpod(keepAlive: true)`:** Use this annotation to create a provider
  that is never destroyed. Ideal for state that should be preserved throughout
  the app's lifecycle, like a user's session or a repository.

```dart
// This provider's state will be discarded when no longer used.
@riverpod
MyController myController(MyControllerRef ref) { ... }

// This provider's state will be kept forever.
@Riverpod(keepAlive: true)
MyRepository myRepository(MyRepositoryRef ref) { ... }
```

##### Optimizing Rebuilds with `select`

If your widget only depends on a small part of a complex state object, use
`select` to prevent unnecessary rebuilds. The widget will only rebuild if the
selected value changes.

```dart
@freezed
class User with _$User {
  factory User({required String name, required int age}) = _User;
}

// Assume userProvider exposes a User object.
// This widget will rebuild ONLY when the user's name changes.
Widget build(BuildContext context, WidgetRef ref) {
  final userName = ref.watch(userProvider.select((user) => user.name));
  return Text(userName);
}
```

### State Management with BLoC

BLoC (Business Logic Component) is a predictable state management library that
helps separate business logic from the UI.

#### Core BLoC Guidelines

- **Immutability:** Events and States must be immutable. Use packages like
  `freezed` to enforce this.
- **Separation:** Keep business logic inside BLoCs/Cubits and UI logic within
  your widgets.
- **`BlocProvider`:** Use `BlocProvider` to create and provide a BLoC/Cubit to
  the widget tree.
- **`BlocBuilder`:** Use to rebuild a widget in response to state changes.
  Scope it to only the widgets that need to rebuild.
- **`BlocListener`:** Use for one-time actions in response to state changes,
  like navigation, showing a `SnackBar`, or a dialog. It is called once per
  state change and should not return a widget.
- **`BlocConsumer`:** Combines `BlocBuilder` and `BlocListener` into one
  widget. Use it when you need to both rebuild the UI and perform an action
  for the same state changes.
- **`BlocObserver`:** Use `BlocObserver` to observe all state changes, events,
  and errors in one place, which is invaluable for debugging.

##### When to Use Cubit vs. BLoC

- **Use `Cubit` for simpler cases:** A `Cubit` exposes direct methods that
  asynchronously emit new states. It's less boilerplate and great when your
  logic is simple.
- **Use `Bloc` for complex cases:** A `Bloc` responds to events, which are
  mapped to states. This is better for complex state machines, advanced use
  cases like event transformation (debouncing), and when you want to trace
  the full interaction history.

**Cubit Example:**

```dart
// Simple, direct method calls
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
  void increment() => emit(state + 1);
}
// In UI: context.read<CounterCubit>().increment();
```

##### Handling One-Time Actions (e.g., SnackBars)

A common mistake is to trigger a `SnackBar` from `BlocBuilder`. This can cause
the `SnackBar` to reappear on every rebuild. Use `BlocListener` to handle
actions that should only happen once per state change.

```dart
// In your BLoC, emit a specific state for the action.
on<FormSubmitted>((event, emit) {
  // ... handle form submission
  emit(state.copyWith(status: FormStatus.success));
});

// In your UI, use BlocListener.
BlocListener<MyFormBloc, MyFormState>(
  listenWhen: (previous, current) => previous.status != current.status,
  listener: (context, state) {
    if (state.status == FormStatus.success) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Success!')),
      );
    }
  },
  child: MyFormWidget(),
)
```

##### Event Transformation (e.g., Debouncing Search Input)

To prevent spamming your backend with requests from a search field, use an
event transformer. The `bloc_concurrency` package is excellent for this.

1. Add the `bloc_concurrency` dependency using the `pub` tool.
2. Apply the `debounce` transformer to your event handler.

```dart
import 'package:bloc_concurrency/bloc_concurrency.dart';

on<SearchTermChanged>(
  (event, emit) async {
    // ... logic to fetch search results
  },
  // This prevents rapid-fire API calls.
  transformer: debounce(const Duration(milliseconds: 300)),
);
```

##### Effective State Modeling with `freezed`

Using `freezed` reduces boilerplate and provides pattern matching (`when`,
`map`), which ensures you handle every possible state.

```dart
// 1. Define your states with freezed
@freezed
class MyState with _$MyState {
  const factory MyState.initial() = _Initial;
  const factory MyState.loading() = _Loading;
  const factory MyState.success(List<Data> data) = _Success;
  const factory MyState.error(String message) = _Error;
}

// 2. Use `when` in your UI for clean, exhaustive state handling
@immutable
class MyWidget extends StatelessWidget {
  MyWidget(this.state);

  final MyState state;

  @override
  Widget build(BuildContext context) {
    return state.when(
      initial: () => const Text('Please start a search.'),
      loading: CircularProgressIndicator.new,
      success: (List<Data> data) => MyDataTable(data: data),
      error: (String message) => Text('Error: $message'),
    );
  }
}
```

## Navigation with go_router

For more complex navigation, deep linking, and web support, the `go_router`
package is a robust and recommended solution. It provides a declarative API for
defining routes and handling navigation.

To use `go_router`, first add it to your `pubspec.yaml` using the `pub` tool's
`add` command.

### Example go_router Configuration

```dart
// In main.dart or a dedicated router.dart file
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

// Define your routes
final GoRouter _router = GoRouter(
  routes: <RouteBase>[
    GoRoute(
      path: '/',
      builder: (BuildContext context, GoRouterState state) {
        return const HomeScreen(); // Your home screen
      },
      routes: <RouteBase>[
        GoRoute(
          path: 'details/:id', // Route with a path parameter
          builder: (BuildContext context, GoRouterState state) {
            final String id = state.pathParameters['id']!;
            return DetailScreen(id: id); // Screen to show details
          },
        ),
        GoRoute(
          path: 'settings',
          builder: (BuildContext context, GoRouterState state) {
            return const SettingsScreen(); // Your settings screen
          },
        ),
      ],
    ),
  ],
);

// In your MaterialApp or CupertinoApp
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: _router,
      title: 'GoRouter Example',
      // ... your theme data
    );
  }
}

// Example screens for the router
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Home Screen')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () => context.go('/details/123'), // Navigate to details with ID
              child: const Text('Go to Details 123'),
            ),
            ElevatedButton(
              onPressed: () => context.go('/settings'), // Navigate to settings
              child: const Text('Go to Settings'),
            ),
          ],
        ),
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  final String id;
  const DetailScreen({super.key, required this.id});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Detail Screen: $id')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => context.pop(), // Pop back
          child: const Text('Go Back'),
        ),
      ),
    );
  }
}

class SettingsScreen extends StatelessWidget {
  const SettingsScreen({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Settings Screen')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => context.pop(), // Pop back
          child: const Text('Go Back'),
        ),
      ),
    );
  }
}
```

### Advanced Features

- **Deep Linking**: `go_router` handles deep links automatically based on the
  defined URL paths, allowing specific screens to be opened directly from
  external sources (e.g., web links, push notifications).
- **Auth Redirects**: Configure `go_router`'s `redirect` property to handle
  authentication flows, ensuring users are redirected to login screens when
  unauthorized, and back to their intended destination after successful login.

## Testing

- **Convention:** Follow the Arrange-Act-Assert (or Given-When-Then) pattern.
- **Unit Tests:** Write unit tests for domain logic, data layer, and state
  management.
- **Widget Tests:** Write widget tests for UI components.
- **Integration Tests:** For broader application validation, use integration
  tests to verify end-to-end user flows.
- **integration_test package** Do not use the discontinued integration_test
  package, use the one that is built into the Flutter SDK. This is one
  instance where you can add a dependency by editing the pubspec.yaml
  directly.
- **Mocks:** Prefer fakes or stubs over mocks. If mocks are absolutely
  necessary, use `mockito` or `mocktail` to create mocks for dependencies.
  While code generation is common for state management (e.g., with `freezed`),
  try to avoid it for mocks.
- **Coverage:** Aim for high test coverage.

## Commit messages

When committing code to a git repo, commit messages should follow the
"Conventional Commits" spec:

1.  The first commit commit description line MUST be prefixed with a type,
    which consists of a noun, `feat`, `fix`, etc., followed by the OPTIONAL
    scope, OPTIONAL `!`, and REQUIRED terminal colon and space.
2.  The type `feat` MUST be used when a commit adds a new feature to your
    application or library.
3.  The type `fix` MUST be used when a commit represents a bug fix for your
    application.
4.  A scope MAY be provided after a type. A scope MUST consist of a noun
    describing a section of the codebase surrounded by parenthesis, e.g.,
    `fix(parser):`
5.  A description MUST immediately follow the colon and space after the
    type/scope prefix. The description is a short summary of the code changes,
    e.g., _fix: array parsing issue when multiple spaces were contained in
    string_.
6.  A longer commit body MAY be provided after the short description, providing
    additional contextual information about the code changes. The body MUST
    begin one blank line after the description.
7.  A commit body is free-form and MAY consist of any number of newline
    separated paragraphs.
8.  One or more footers MAY be provided one blank line after the body. Each
    footer MUST consist of a word token, followed by either a `:<space>` or
    `<space>#` separator, followed by a string value.
9.  A footer’s token MUST use `-` in place of whitespace characters, e.g.,
    `Acked-by` (this helps differentiate the footer section from a
    multi-paragraph body).
10. A footer’s value MAY contain spaces and newlines, and parsing MUST
    terminate when the next valid footer token/separator pair is observed.
11. Breaking changes MUST be indicated in the type/scope prefix of a commit,
    or as an entry in the footer.
12. If included as a footer, a breaking change MUST consist of the uppercase
    text BREAKING-CHANGE, followed by a colon, space, and description, e.g.,
    _BREAKING-CHANGE: environment variables now take precedence over config
    files_.
13. If included in the type/scope prefix, breaking changes MUST be indicated by
    a `!` immediately before the `:`, and the commit description SHALL be used
    to describe the breaking change. e.g.: _fix(parser)!: Stop allowing env
    vars to overrride config files. Using env vars will break._
14. Types other than `feat` and `fix` MAY be used in your commit messages,
    e.g., _docs: update ref docs._
15. The units of information that make up Conventional Commits MUST NOT be
    treated as case sensitive, with the exception of BREAKING-CHANGE which
    MUST be uppercase.

## Documentation

- **`dartdoc`:** Write `dartdoc`-style comments for all public APIs.

### Documentation Philosophy

- **Comment wisely:** Use comments to explain why the code is written a certain
  way, not what the code does. The code itself should be self-explanatory.
- **Document for the user:** Write documentation with the reader in mind. If
  you had a question and found the answer, add it to the documentation where
  you first looked. This ensures the documentation answers real-world
  questions.
- **No useless documentation:** If the documentation only restates the obvious
  from the code's name, it's not helpful. Good documentation provides context
  and explains what isn't immediately apparent.
- **Consistency is key:** Use consistent terminology throughout your
  documentation.

### Commenting Style

- **Use `///` for doc comments:** This allows documentation generation tools to
  pick them up.
- **Start with a single-sentence summary:** The first sentence should be a
  concise, user-centric summary ending with a period.
- **Separate the summary:** Add a blank line after the first sentence to create
  a separate paragraph. This helps tools create better summaries.
- **Avoid redundancy:** Don't repeat information that's obvious from the code's
  context, like the class name or signature.
- **Don't document both getter and setter:** For properties with both, only
  document one. The documentation tool will treat them as a single field.

### Writing Style

- **Be brief:** Write concisely.
- **Avoid jargon and acronyms:** Don't use abbreviations unless they are widely
  understood.
- **Use Markdown sparingly:** Avoid excessive markdown and never use HTML for
  formatting.
- **Use backticks for code:** Enclose code blocks in backtick fences, and
  specify the language.

### What to Document

- **Public APIs are a priority:** Always document public APIs.
- **Consider private APIs:** It's a good idea to document private APIs as well.
- **Library-level comments are helpful:** Consider adding a doc comment at the
  library level to provide a general overview.
- **Include code samples:** Where appropriate, add code samples to illustrate
  usage.
- **Explain parameters, return values, and exceptions:** Use prose to describe
  what a function expects, what it returns, and what errors it might throw.
- **Place doc comments before annotations:** Documentation should come before
  any metadata annotations.
