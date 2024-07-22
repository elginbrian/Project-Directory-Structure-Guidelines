# Project Directory Structure Guidelines

## Overview

This project directory structure outlines a well-organized architecture for a Kotlin-based Android application using modern development practices like Clean Architecture, MVVM, and Jetpack Compose.

## Directory Breakdown

```
├── java/com/app_affiliation/app_name
│   ├── data
│   │   ├── local
│   │   │   ├── UserDao.kt
│   │   │   ├── AppDatabase.kt
│   │   │   └── UserLocalDataSource.kt
│   │   ├── remote
│   │   │   ├── UserService.kt
│   │   │   └── UserRemoteDataSource.kt
│   │   ├── repository
│   │   │   └── UserRepositoryImpl.kt
│   │   └── mapper
│   │       └── UserMapper.kt
│   ├── di
│   │   └── Module.kt
│   ├── domain
│   │   ├── model
│   │   │   └── User.kt
│   │   ├── repository
│   │   │   └── UserRepository.kt
│   │   └── usecase
│   │       └── GetUserUseCase.kt
│   ├── presentation
│   │   ├── screen
│   │   │   ├── UserScreen.kt
│   │   │   ├── UserDetailScreen.kt
│   │   │   └── UserListScreen.kt
│   │   ├── viewmodel
│   │   │   └── UserViewModel.kt
│   │   ├── components
│   │   │   ├── design_system
│   │   │   │   ├── Color.kt
│   │   │   │   ├── Typography.kt
│   │   │   │   └── Theme.kt
│   │   │   ├── widget
│   │   │   │   └── CustomWidget.kt
│   │   │   └── navigation
│   │   │       ├── NavGraph.kt
│   │   │       ├── AuthNavHost.kt
│   │   │       └── HomeNavHost.kt
│   └── utils
│       └── Tools.kt
```

## Components

### data

Handles all data-related operations, ensuring a clear separation between local and remote data sources.

- **local**: For local database interactions using Room.
  - `UserDao.kt`: Defines DAO (Data Access Object) for user-related database operations, specifying SQL queries and database operations.
  - `AppDatabase.kt`: The Room database class, serving as the main access point to the persisted data.
  - `UserLocalDataSource.kt`: Implementation of the data source for local storage, managing data operations with the local database.
- **remote**: For network interactions, handling data fetched from external APIs.
  - `UserService.kt`: Defines API service interfaces for network operations, specifying endpoints and HTTP methods.
  - `UserRemoteDataSource.kt`: Implementation of the data source for remote storage, managing data operations with the network service.
- **repository**: Implements the repository pattern, providing a clean API for data access to the rest of the application.
  - `UserRepositoryImpl.kt`: Combines data from local and remote sources, ensuring seamless data access and synchronization.
- **mapper**: Handles data transformation, converting data between different layers of the application.
  - `UserMapper.kt`: Maps data between data transfer objects (DTOs), domain models, and database entities.

### di

Dependency injection setup using Koin, facilitating the injection of dependencies throughout the application.

- `Module.kt`: Provides app-wide dependencies, including network services, databases, and repositories.

### domain

Contains business logic and domain models, representing the core functionality of the application.

- **model**: Domain models representing the main data structures used throughout the application.
  - `User.kt`: Represents the user domain model, defining user properties and methods.
- **repository**: Domain repository interfaces, abstracting data access for domain use cases.
  - `UserRepository.kt`: Interface for the user repository, specifying methods for data operations.
- **usecase**: Use cases encapsulating business logic, coordinating data retrieval and processing.
  - `GetUserUseCase.kt`: Handles the logic for retrieving user data, interacting with repositories and data sources.

### presentation

Manages UI components and interactions, using Jetpack Compose for declarative UI design.

- **screen**: Defines screens using Jetpack Compose, creating composable functions for different user-related screens.
  - `UserScreen.kt`: Composable function for the user screen, displaying user information and interactions.
  - `UserDetailScreen.kt`: Composable function for the user detail screen, showing detailed user information.
  - `UserListScreen.kt`: Composable function for the user list screen, listing multiple users.
- **viewmodel**: ViewModel classes for managing UI-related data, providing data to the UI and handling user interactions.
  - `UserViewModel.kt`: Manages data for user-related UI components, interacting with use cases and repositories.
- **components**: Custom UI components, enhancing the visual and interactive elements of the application.
  - **design_system**: UI design elements, including color and typography definitions.
    - `Color.kt`: Defines color values used throughout the application.
    - `Typography.kt`: Defines typography styles used in the application.
    - `Theme.kt`: Defines the overall theme and style settings for Compose.
  - **widget**: Specific custom widgets, providing reusable UI components for various needs.
    - `CustomWidget.kt`: Custom widget for specific UI requirements, enhancing user experience.
  - **navigation**: Navigation setup using Jetpack Compose, managing app navigation and routing.
    - `NavGraph.kt`: Defines the navigation graph for the application, specifying navigation routes and destinations.
    - `AuthNavHost.kt`: Manages authentication-related navigation, handling routes for login and registration.
    - `HomeNavHost.kt`: Manages home-related navigation, handling routes for the main app screens.

### utils

General utility classes and functions, providing helper methods and extensions to simplify common tasks.

- `Tools.kt`: Helper methods to simplify common tasks.
