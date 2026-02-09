## Project Overview

StepEeeasy (package: `com.example.stepeeeasy`) is an Android walking tracker application built with Jetpack Compose and Material Design 3. The app records walk sessions with step counting, distance tracking, and GPS path visualization. All data is stored locally for privacy and offline functionality.

**Target Platform:** Android API 34+ (minSdk: 34, targetSdk: 36)
**Language:** Kotlin
**UI Framework:** Jetpack Compose with Material 3
**Architecture Goal:** Clean Architecture + Repository Pattern (see docs/android-development-plan.md)

## Build Commands

### Standard Development
```bash
# Build debug APK
./gradlew assembleDebug

# Build release APK
./gradlew assembleRelease

# Install on connected device
./gradlew installDebug

# Run unit tests
./gradlew test

# Run instrumented tests (requires device/emulator)
./gradlew connectedAndroidTest

# Clean build artifacts
./gradlew clean

# Run lint checks
./gradlew lint
```

### Running Tests
```bash
# Run all tests
./gradlew test connectedAndroidTest

# Run only unit tests
./gradlew testDebugUnitTest

# Run specific test class
./gradlew test --tests com.example.stepeeeasy.ExampleUnitTest
```

### Gradle Tasks
```bash
# List all available tasks
./gradlew tasks

# Check dependencies
./gradlew dependencies

# Generate build reports
./gradlew build --scan
```
## Architecture & Code Structure

### Current State (Early Stage)
The project is in early development with basic scaffolding. The current implementation includes:
- **MainActivity.kt**: Entry point with adaptive navigation scaffold
- **Bottom Navigation**: Home, History, and Paths screens
- **Settings**: Accessed via gear icon in Home screen (not in bottom nav)
- **Compose UI Theme**: Material 3 theming with dynamic color support (Android 12+)
- **Database**: Room database with WalkEntity and GpsPointEntity
- **DI**: Hilt dependency injection configured

- ### Planned Architecture (Clean Architecture)
The comprehensive architecture plan is documented in `docs/android-development-plan.md`. Key layers:

1. **Presentation Layer** (`presentation/`):
   - Composables for Home, History, Paths, and Settings screens
   - ViewModels with LiveData/Flow for state management
   - Navigation management

2. **Domain Layer** (`domain/`):
   - Business logic and use cases (StartWalkUseCase, StopWalkUseCase, etc.)
   - Domain models (Walk, GpsPoint, DailyStats)
   - Repository interfaces

3. **Data Layer** (`data/`):
   - Room database with WalkEntity and GpsPointEntity
   - Repository implementations
   - Local data storage with DataStore for settings

4. **Service Layer** (`service/`):
   - WalkTrackingService (foreground service for background tracking)
   - SensorManager (step counter integration)
   - GpsLocationManager (location tracking)

5. **Dependency Injection** (`di/`):
   - Hilt modules for dependency injection

### Database Schema
Room database with two main entities:
- **WalkEntity**: Stores walk sessions (start_time, end_time, total_steps, distance_meters, is_active, date)
- **GpsPointEntity**: Stores GPS coordinates for path visualization (walk_id FK, latitude, longitude, timestamp, accuracy)

## Development Guidelines

### Compose UI Patterns
- Use `@PreviewScreenSizes` for responsive design testing
- Leverage Material 3 adaptive components (NavigationSuiteScaffold)
- Follow the theme defined in `ui/theme/Theme.kt` with dynamic color support
- State management with `rememberSaveable` for configuration changes

### Architecture Implementation
When adding features, follow this structure:
1. Define domain model in `domain/model/`
2. Create repository interface in `domain/repository/`
3. Implement repository in `data/repository/`
4. Create use case in `domain/usecase/`
5. Build ViewModel in `presentation/[screen]/`
6. Implement Composable UI in `presentation/[screen]/`

### Sensor & Permission Handling
- Step counting requires `ACTIVITY_RECOGNITION` permission (API 29+)
- GPS tracking requires `ACCESS_FINE_LOCATION` and `ACCESS_COARSE_LOCATION`
- Background tracking requires `FOREGROUND_SERVICE` permission
- Always request runtime permissions before accessing sensors
- Use foreground service with notification for continuous tracking

### Data Persistence
- Use Room for structured data (walks, GPS points)
- Use DataStore (Preferences) for settings (user height, permissions)
- Always use Flow for reactive database queries
- Perform database operations on IO dispatcher

### Testing Strategy
- Unit tests for ViewModels, UseCases, and Utilities (e.g., DistanceCalculator)
- Instrumented tests for Repository and DAO implementations
- Test files located in:
  - `app/src/test/` for unit tests
  - `app/src/androidTest/` for instrumented tests
