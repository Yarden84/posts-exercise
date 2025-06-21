# Posts Exercise

A Vue 3 application for displaying and managing posts with search, sorting, and CRUD (except D-deleting) functionality.

## Features

- **Post Display**: View posts with infinite scroll pagination
- **Search**: Search posts by title or content
- **Sorting**: Sort posts by ID, title, or other criteria
- **CRUD Operations**: Add, edit, and manage posts
- **Responsive Design**: Mobile-friendly interface
- **Real-time Updates**: Dynamic content updates without page refresh

### Important Note: Frontend-Only Operations

⚠️ **CRUD operations are currently implemented only on the frontend and do not persist to the backend API.** This means:

- **Added posts** will only appear in the current session and will be lost on page refresh
- **Edited posts** will revert to their original state on page refresh
- **New/edited posts** will not appear in search results or be affected by sorting operations, as these operations query the original API data
- **Data persistence** is not implemented - all changes are temporary

## Tech Stack

- **Frontend Framework**: Vue 3 with Composition API
- **Build Tool**: Vite
- **Styling**: SCSS
- **API**: DummyJSON API for posts data
- **Development Tools**: Vue DevTools integration

## Prerequisites

- Node.js (version 16 or higher)
- npm or yarn package manager

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd posts-exercise
```

2. Install dependencies:
```bash
npm install
```

## Development

### Start Development Server

```bash
npm run dev
```

The application will be available at `http://localhost:5173` (or the port shown in your terminal).

### Build for Production

```bash
npm run build
```

### Preview Production Build

```bash
npm run preview
```

## Project Structure

src/
├── components/ # Vue components
│ ├── Navbar.vue # Navigation and search/sort controls
│ ├── PostsList.vue # Main posts display with infinite scroll
│ ├── Post.vue # Individual post component
│ ├── PostFormModal.vue # Add/edit post modal
│ ├── SearchPosts.vue # Search functionality
│ └── SortPosts.vue # Sorting controls
├── assets/ # Static assets
├── App.vue # Main application component
└── main.js # Application entry point