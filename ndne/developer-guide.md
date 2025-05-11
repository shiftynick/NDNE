# NodeBB Developer Guide

This guide provides an overview of the NodeBB project structure, architecture, and development practices to help you get started with contributing or building your own features, plugins, and themes.

## Project Overview

NodeBB is a sophisticated forum platform built on Node.js.

*   **Backend:**
    *   **Framework:** Express.js for routing, middleware, and handling HTTP requests.
    *   **Language:** JavaScript (Node.js).
    *   **Core Logic:** Located in `src/`, with a modular structure separating concerns like database interaction (`src/database`), controllers (`src/controllers`), routing (`src/routes`), real-time communication (`src/socket.io`), plugin management (`src/plugins`), user management (`src/user`), etc.
    *   **Database:** Supports MongoDB and PostgreSQL, with Redis for caching, sessions, and Socket.IO scaling. An abstraction layer likely exists in `src/database`.
    *   **Real-time:** Socket.IO is integral for features like chat, live notifications, and real-time updates.
    *   **Extensibility:** A robust plugin system (hooks, filters) and theme engine are core to its design.
*   **Frontend:**
    *   **Language:** JavaScript (likely modern ES6+), HTML, SASS/CSS.
    *   **Bundling:** Webpack is used to manage and bundle frontend assets (`public/src`, `public/scss`).
    *   **Templating:** BenchpressJS (NodeBB's custom templating engine) is used to render views. Templates are likely located in theme directories and core template folders (e.g., `public/templates` or `src/views` if they serve master layouts).
    *   **Client-side Interaction:** jQuery is present, along with other libraries for UI components and interactions.
*   **Communication:**
    *   Standard HTTP/S requests for page loads and API calls.
    *   WebSocket (via Socket.IO) for real-time data exchange.
*   **Development & Build:**
    *   Managed via `npm` scripts (`package.json`).
    *   CLI tool (`./nodebb`) for various operations.
    *   Linting with ESLint, testing with Mocha.
    *   Webpack for frontend asset compilation.
*   **Configuration:**
    *   Mainly through `config.json`.
    *   Environment variables can also influence behavior.

## Directory Structure Highlights

*   **`src/`**: Core server-side source code.
    *   `admin/`: Admin Control Panel (ACP) logic.
    *   `api/`: Backend API logic.
    *   `categories/`, `topics/`, `posts/`: Core forum functionalities.
    *   `controllers/`: Handles incoming web requests.
    *   `database/`: Database interaction layer.
    *   `middleware/`: Express middleware.
    *   `plugins/`: Core plugin system logic.
    *   `routes/`: URL route definitions.
    *   `socket.io/`: Server-side Socket.IO event handling.
    *   `user/`: User management, authentication.
    *   `webserver.js`: Express web server setup.
    *   `start.js`: Application startup sequence.
*   **`public/`**: Client-side assets.
    *   `src/`: Primary frontend JavaScript source.
    *   `scss/`: SCSS files for styling.
    *   `language/`: Frontend internationalization files.
    *   `templates/`: Core BenchpressJS templates (though most templates reside in themes).
*   **`node_modules/`**: Project dependencies, including installed plugins and themes.
*   **`install/`**: Files related to NodeBB installation and setup.
*   **`test/`**: Automated tests.
*   **`build/`**: Output for compiled/minified assets.
*   **`.vscode/launch.json`**: VS Code debugger configuration.
*   **`package.json`**: Project metadata, dependencies, and npm scripts.
*   **`app.js`**: Main application entry point (module-wise, though `loader.js` is used by `npm start`).
*   **`loader.js`**: Used by `npm start` for initializations.
*   **`nodebb` / `nodebb.bat`**: Command-line interface scripts.
*   **`config.json`**: Application configuration file.

## Development Guide

### 1. Setup & Environment

*   **Prerequisites:**
    *   Node.js (version specified in `package.json`'s `engines` field, currently `>=20`).
    *   A supported database: PostgreSQL or MongoDB.
    *   Redis.
    *   Docker is recommended for easier setup of databases/Redis (refer to `NDNE-README.md` or the project's main `README.md`).
*   **Clone Repository:**
    ```bash
    git clone <repository_url>
    cd <repository_directory>
    ```
*   **Install Dependencies:**
    ```bash
    npm install
    ```
*   **Configuration (`config.json`):**
    *   If `config.json` is missing or incomplete, running `./nodebb setup` (or `./nodebb start` for the first time) will launch the web-based installation wizard. This process creates/updates `config.json` with your database details, admin user, etc.
    *   For development, ensure `config.json` points to your development database and Redis instances.
*   **VS Code Debugging:**
    *   Ensure `.vscode/launch.json` is configured (as previously discussed).
    *   This allows you to launch NodeBB in development mode directly from VS Code and use its debugging features.

### 2. Running for Development

*   **Using the CLI (Development Mode):**
    ```bash
    ./nodebb dev
    ```
    This command:
    *   Starts NodeBB in development mode.
    *   Enables verbose logging.
    *   Often disables or adjusts asset minification (e.g., provides source maps).
    *   May watch for file changes and trigger automatic rebuilds/restarts.
*   **Using VS Code Debugger:**
    *   Select the "NodeBB Dev" configuration from the "Run and Debug" panel and click Start.
*   **Accessing the Forum:**
    *   By default, NodeBB is accessible at `http://localhost:4567` (this can be configured).

### 3. Key Development Tasks & Where to Look

*   **Adding/Modifying Backend Features:**
    *   **Routes:** Define new HTTP routes or modify existing ones in `src/routes/`.
    *   **Controllers:** Implement the logic for these routes in `src/controllers/`.
    *   **Models/Services (Data Logic):** For database interactions, refer to modules like `src/posts.js`, `src/topics.js`, `src/user.js`, `src/groups.js`, etc. For lower-level DB operations, see `src/database/`.
    *   **Sockets (Real-time):** Implement real-time aspects in `src/socket.io/`.
    *   **Plugins:** For new, distinct functionality, creating a plugin is often the best approach (see "Plugin Development" below).
*   **Frontend Changes (UI/UX):**
    *   **Templates (`.tpl` files):** These use BenchpressJS. Core templates are in `public/templates/`, but most are located within theme directories (e.g., `node_modules/nodebb-theme-persona/templates/`).
    *   **Client-side JavaScript:** Edit files in `public/src/`. Webpack will bundle these. Key entry points or modules specific to forum sections are found here.
    *   **Styles (SASS/CSS):** Core styles are in `public/scss/`. Theme-specific styles are within theme directories (e.g., `node_modules/nodebb-theme-persona/scss/`).
*   **Creating/Modifying APIs:**
    *   Work within `src/api/` and relevant controller files (e.g., `src/controllers/api.js`).
    *   Follow existing patterns for API routing, request handling, and response formatting.
*   **Database Schema Changes/Migrations:**
    *   Upgrade scripts, often containing schema modifications or data migrations, are in `src/upgrades/`. These run when NodeBB detects a version change.
    *   Ensure new data fields are correctly handled in all relevant database interaction logic.

### 4. Plugin Development

NodeBB's extensibility largely comes from its plugin system. Plugins can add routes, modify templates, listen to events (hooks), and much more.

*   **Structure:**
    *   `plugin.json`: Manifest file describing the plugin, its hooks, etc.
    *   `library.js` (conventionally): Main server-side JavaScript file for the plugin.
    *   Plugins can also include client-side JS, SASS/CSS files, and templates.
*   **Hooks:** Plugins interact with NodeBB core by listening to hooks (events).
    *   Search the codebase for `plugins.hooks.fire` (for actions) or `plugins.hooks.filter` (for data modification) to discover available hooks.
    *   The official NodeBB documentation is also a good resource for hook information.
*   **Examples:** Examine existing plugins in `node_modules/nodebb-plugin-*` for practical examples.
*   **CLI for Plugins:**
    ```bash
    ./nodebb activate nodebb-plugin-myplugin
    ./nodebb deactivate nodebb-plugin-myplugin
    # and other management commands
    ```

### 5. Theme Development

Themes control the visual appearance and layout of the forum.

*   **Structure:**
    *   `theme.json`: Manifest file for the theme.
    *   Primarily consists of BenchpressJS templates (`.tpl`), SASS/CSS files, and client-side JavaScript.
*   **Base Themes:** Themes can inherit from a base theme (e.g., `nodebb-theme-persona` is a common choice).
*   **Examples:** Look at `node_modules/nodebb-theme-*` (e.g., `nodebb-theme-persona`).

### 6. Building Assets

While `./nodebb dev` usually handles asset building during development (often with hot-reloading or auto-rebuilds), you might need to run manual builds, especially for production or testing production-like builds.

*   **Manual Build:**
    ```bash
    ./nodebb build
    ```
    This command typically:
    *   Compiles SASS to CSS.
    *   Bundles client-side JavaScript using Webpack.
    *   Minifies assets (for production builds).

### 7. Testing

NodeBB uses Mocha for its test suite.

*   **Run Tests:**
    ```bash
    npm test
    ```
*   **Writing Tests:**
    *   Add new tests to the `test/` directory.
    *   Follow the patterns of existing tests for controllers, helpers, socket interactions, etc.

### 8. Coding Style & Linting

*   **ESLint:** NodeBB uses ESLint for code linting. The configuration is in `eslint.config.mjs`.
*   **Run Linter:**
    ```bash
    npm run lint
    ```
*   **Editor Integration:** Configure your code editor to use the project's ESLint setup for real-time feedback and auto-fixing where possible.
*   **Contribution Guidelines:** Refer to `.github/CONTRIBUTING.md` for general guidelines on contributing to NodeBB.

### 9. Important Files & Concepts to Understand Early

*   **Startup Flow:** `loader.js` -> `app.js` -> `src/start.js`.
*   **Web Server Config:** `src/webserver.js`.
*   **Main Routes:** `src/routes/index.js`.
*   **Plugin System Core:** `src/plugins.js` (especially `src/plugins/hooks.js`).
*   **Database Abstraction:** `src/database/` (and specific implementations like `mongo.js`, `postgres.js`, `redis.js`).
*   **BenchpressJS Templating:** Familiarize yourself with its syntax if doing frontend/theme work.
*   **Socket.IO Event Handling:** Understand how real-time events are defined and handled on both client (`public/src/modules/socket.js` or similar) and server (`src/socket.io/`).

This guide should provide a solid starting point. The best way to learn the codebase is to explore it, trace feature implementations, and start with small, focused tasks. 