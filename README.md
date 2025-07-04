
# Image Colorization with Neural Networks - SvelteKit Frontend

> A deep learning application that colorizes grayscale images, deployed on Kubernetes with a full CI/CD pipeline.


This repository contains the code for the frontend of the Image Colorization project, built with SvelteKit. The frontend interacts with a FastAPI backend that uses a Conditional Generative Adversarial Network (cGAN) to colorize grayscale images.
---

| **Live Demo** |
| :---:|
| [**https://colorization.dyn.cloud.e-infra.cz/**](https://colorization.dyn.cloud.e-infra.cz/) |


## 🌟 Project Overview
I wanted this project to be complete, end-to-end process which goes beyond just training a deep learning model. The goal is to bring the model to user-facing frontend in a scalable and devops-focused way. It takes a grayscale image from a user, uses a Conditional Generative Adversarial Network (cGAN) to add realistic color, and returns the result. The entire system is containerized and deployed on a public Kubernetes cluster using Helm, with automated builds and deployments managed by GitHub Actions.

## 📂 Project Repositories
This project is split into four repositories, each with a specific responsibility:

| Repository | Description | Link |
| :--- | :--- | :---: |
| 🎨 **Frontend** | SvelteKit user interface. | **((This repository))** |
| 🚀 **Backend** | FastAPI application handling API requests. | [image-colorizer-backend](https://github.com/vojtam/colorization-backend) |
| 🧠 **Model Training**| Scripts, notebooks, and code for training the cGAN model. | [image-colorizer-training](https://github.com/vojtam/cGAN-image-colorization) |
| ⚙️ **Infrastructure**| Kubernetes manifests, Helm charts, and CI/CD workflows. | [image-colorizer-infra](https://github.com/vojtam/colorization-helm-manifests)|


## Developing

Once you've created cloned the project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.
