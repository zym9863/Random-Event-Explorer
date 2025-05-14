# Random Event Explorer

English | [简体中文](./README.md)

An interactive random event simulation and probability calculation tool developed with Vue 3 + TypeScript + Vite.

## Project Introduction

Random Event Explorer is an interactive application for exploring the mysteries of the probability world. Through large-scale simulations, it helps users observe statistical patterns of random events and intuitively understand basic concepts of probability theory.

## Main Features

### Random Event Simulation Laboratory

- Supports multiple types of random events: coin flips, dice rolls, card draws
- Customizable number of simulations
- Provides multiple result display methods:
  - Simulation results list
  - Frequency statistics table
  - Frequency distribution chart

### Probability Calculator

- Provides common probability calculation tools
- Supports various probability distributions
- Intuitively displays calculation results

## Technology Stack

- **Frontend Framework**: Vue 3
- **Development Language**: TypeScript
- **Build Tool**: Vite
- **Styling**: CSS (Scoped CSS)

## Project Structure

```
src/
├── assets/        # Static resources
├── components/    # Components
│   ├── HelloWorld.vue
│   ├── ProbabilityCalculator.vue
│   └── RandomEventLab.vue
├── App.vue        # Main application component
├── main.ts        # Entry file
└── style.css      # Global styles
```

## Development Guide

### Requirements

- Node.js 14.0+
- npm or yarn

### Installing Dependencies

```bash
npm install
# or
yarn
```

### Development Server

```bash
npm run dev
# or
yarn dev
```

### Building for Production

```bash
npm run build
# or
yarn build
```

## Learn More

- [Vue 3 Documentation](https://v3.vuejs.org/)
- [Vue 3 `<script setup>` SFC Documentation](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup)
- [Vue TypeScript Guide](https://vuejs.org/guide/typescript/overview.html#project-setup)
