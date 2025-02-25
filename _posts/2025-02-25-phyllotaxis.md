---
title: "Phyllotaxis"
date: 2025-02-25
layout: post
---

# Introduction

Phyllotaxis, derived from the Greek words "phyllon" (leaf) and "taxis" (arrangement), is the study of the spatial patterns in which leaves, seeds, or other botanical structures grow. These patterns are not just aesthetically pleasing; they are also a result of optimized growth strategies in nature. For instance, the spiral arrangement of sunflower seeds maximizes packing efficiency, allowing the plant to use space and resources effectively.

At its core, phyllotaxis reveals the interplay between biological processes and mathematical principles. The golden angle—approximately 137.5°—and Fibonacci numbers frequently appear in phyllotaxis, creating spirals that are both functional and mesmerizing. These principles not only enhance our understanding of plant growth but also inspire applications in art, architecture, and computational modeling.

In this guide, we will delve into phyllotaxis algorithms and their visualization using JavaScript and three.js. Starting with the planar model and expanding into three-dimensional variations, we’ll explore how these algorithms recreate nature's patterns with remarkable precision. Whether you’re a botanist, a programmer, or simply a lover of nature’s beauty, this journey through the mathematics of growth promises to be an engaging exploration.

## The Planar Model

### The Basics

Phyllotaxis patterns often start with a two-dimensional representation, capturing the essence of how plants arrange structures like seeds or leaves in a flat spiral. The planar phyllotaxis algorithm models this arrangement using simple polar coordinates. Each point on the spiral represents an "organ"—a seed, leaf, or similar unit—calculated based on its position in the sequence, a divergence angle, and a scaling factor.

The divergence angle plays a pivotal role in determining the spiral's appearance. The golden angle, approximately 137.5°, is the most commonly used value. This angle ensures a distribution that avoids overlaps while maintaining symmetry, a phenomenon seen in sunflowers and daisies. The radius at which each point is placed grows proportionally to the square root of its index, creating the outward expansion typical of natural spirals.

```javascript
/**
 * Planar phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.radiusConstant - The radius constant for the phyllotaxis arrangement.
 * @return {Object[]} - An array of 2D points (with z = 0) representing the phyllotaxis arrangement.
 */
const planarPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), radiusConstant}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radius = radiusConstant * Math.sqrt(index);
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: 0,
    });
  }
  return points;
};

planarPhyllotaxis({organs: 100, radiusConstant: 1.2})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="ByaKrVp" data-pen-title="Planar phyllotaxis - basic" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/ByaKrVp">
  Planar phyllotaxis - basic</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

Phyllotaxis patterns are as versatile as they are beautiful, and small tweaks to the base algorithm can create entirely new visual effects. In the next sections, we explore modifications that add depth and flexibility to the planar model, including creating an empty central area, constraining the outer radius, controlling point distribution and adding depth.

### Introducing an Empty Area Around the Center

In nature, certain phyllotaxis patterns feature a central void, such as the hollow center of some flower heads. To simulate this, we can introduce an inner radius parameter to the algorithm. This shifts the starting point of the spiral outward, leaving a circular empty area in the center. The inner radius can be adjusted to control the size of the void.

This modification is especially useful for creating patterns that mimic plant species with defined central structures, or for artistic purposes where a focal point is desired. The algorithm remains fundamentally the same, but the inclusion of the inner radius parameter highlights the adaptability of the model.

```javascript
/**
 * Planar phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} [options.innerRadius=0] - Distance from the center at which the first organ is placed. Defaults to 0.
 * @param {number} options.radiusConstant - The radius constant for the phyllotaxis arrangement.
 * @return {Object[]} - An array of 2D points (with z = 0) representing the phyllotaxis arrangement.
 */
const planarPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), innerRadius = 0, radiusConstant}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radius = innerRadius + radiusConstant * Math.sqrt(index);
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: 0,
    });
  }
  return points;
};

planarPhyllotaxis({organs: 100, innerRadius: 5, radiusConstant: 1})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="ByaKrGW" data-pen-title="Planar phyllotaxis - inner radius" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/ByaKrGW">
  Planar phyllotaxis - inner radius</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Constraining the Outer Radius of the Pattern

Without constraints, the size of the spiral expands with the number of points (organs), which may not always be desirable. To maintain a fixed boundary for the pattern, we can introduce an outer radius parameter. By adjusting the scaling factor (radius constant), we ensure that the spiral grows proportionally while remaining within the specified boundary.

This feature is particularly useful for applications where the pattern must fit within a defined area, such as digital art, data visualization, or physical designs. It provides a balance between organic growth and structural control.

```javascript
/**
 * Planar phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} [options.innerRadius=0] - Distance from the center at which the first organ is placed. Defaults to 0.
 * @param {number} options.outerRadius - The outer radius of the generated pattern. outerRadius must be grater than innerRadius.
 * @return {Object[]} - An array of 2D points (with z = 0) representing the phyllotaxis arrangement.
 */
const planarPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), innerRadius = 0, outerRadius}) => {
  const points = [];
  const radiusConstant = (outerRadius - innerRadius) / Math.sqrt(organs - 1);
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radius = innerRadius + radiusConstant * Math.sqrt(index);
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: 0,
    });
  }
  return points;
};

planarPhyllotaxis({organs: 100, innerRadius: 10, outerRadius: 25})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="MYWyVxB" data-pen-title="Planar phyllotaxis - fixed radius" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/MYWyVxB">
  Planar phyllotaxis - fixed radius</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

The image above includes a circle with a radius equal to the outer radius of the pattern. This illustrates the fact that none of the sphere center points extend outside of the outer radius.

### Changing the Point Distribution

The current planar algorithm distributes points evenly based on the square root of their index. However, by altering this distribution, we can create unique patterns that vary in density. Introducing a `distribution` parameter allows us to control how the radius changes with the index, producing a wide range of effects.

For example:

- A higher distribution value (in the 0.5 - 1 interval) concentrates points toward the center, resembling a dense cluster.
- A lower distribution value (in the 0 - 0.5 interval) pushes points toward the edges, creating a sparse core and a denser periphery.
These variations mimic different natural growth patterns and provide creative flexibility, enabling simulations of plants with distinct structural properties or artistic patterns with tailored aesthetics.

```javascript
/**
 * Planar phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} [options.innerRadius=0] - Distance from the center at which the first organ is placed. Defaults to 0.
 * @param {number} options.outerRadius - The total radius of the generated pattern. outerRadius must be grater than innerRadius.
 * @param {number} [options.distribution=0.5] - The organ distribution. Defaults to 0.5.
 * @return {Object[]} - An array of 2D points (with z = 0) representing the phyllotaxis arrangement.
 */
const planarPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), innerRadius = 0, outerRadius, distribution = 0.5}) => {
  const points = [];
  const radiusConstant = (outerRadius - innerRadius) / Math.pow(organs - 1, distribution);
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radius = innerRadius + radiusConstant * Math.pow(index, distribution);
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: 0,
    });
  }
  return points;
};

planarPhyllotaxis({organs: 100, outerRadius: 15, distribution: 0.7})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="zxYqWVb" data-pen-title="Planar phyllotaxis - distribution" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/zxYqWVb">
  Planar phyllotaxis - distribution</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Adding Depth

While planar phyllotaxis provides a beautiful two-dimensional representation, many natural structures grow in three dimensions. Adding depth to the pattern involves introducing a vertical dimension, which can simulate the curvature or height variations found in these real-world examples.

One approach is to gradually increase the z-coordinate of each organ in the pattern, creating a gentle slope or dome-like shape. For instance, the z-value can follow a quadratic function of the organ index, forming a parabolic curve that resembles the natural curvature of a flower head. This subtle addition adds realism and complexity to the visualizations.

Depth also opens the door to creative exploration. Depending on the mathematical function applied to the z-axis, the pattern can take on diverse forms, from smooth domes to exaggerated peaks or even wave-like surfaces. These variations allow the algorithm to simulate a broader range of natural growth forms or to serve as a foundation for artistic designs.

```javascript
/**
 * Planar phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} [options.innerRadius=0] - Distance from the center at which the first organ is placed. Defaults to 0.
 * @param {number} options.outerRadius - The total radius of the generated pattern. outerRadius must be grater than innerRadius.
 * @param {number} [options.height=0] - The max height of the organs in the arrangement.
 * @param {number} [options.distribution=0.5] - The organ distribution.
 * @return {Object[]} - An array of 2D points representing the phyllotaxis arrangement.
 */
const planarPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), innerRadius = 0, outerRadius, height = 0, distribution = 0.5}) => {
  const points = [];
  const radiusConstant = (outerRadius - innerRadius) / Math.pow(organs - 1, distribution);
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radius = innerRadius + radiusConstant * Math.pow(index, distribution);
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: - (height / Math.pow(organs - 1, 2) * Math.pow(index, 2)),
    });
  }
  return points;
};

planarPhyllotaxis({organs: 300, outerRadius: 25, height: 7})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="dPyMeyY" data-pen-title="Planar phyllotaxis - depth" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/dPyMeyY">
  Planar phyllotaxis - depth</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

## The Cylindrical Model

The cylindrical phyllotaxis model extends the planar model along a vertical axis, resulting in points arranged around the surface of a cylinder. This model is particularly suited for simulating the growth of columnar plants, such as bamboo, cacti, or certain types of algae. Each organ is defined by its angular position on the circular cross-section and its height along the cylinder.

The simplicity of this model lies in maintaining a constant radius for the cylinder while incrementing the z-coordinate for each organ. The result is a regular, helical arrangement that mimics the upward growth of plants. This straightforward approach also serves as a foundation for more complex modifications, such as introducing variations in radius or height.

```javascript
/**
 * Cylindrical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.radius - The cylinder radius.
 * @param {number} options.height - The cylinder height.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const cylindricalPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), radius, height}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    points.push({
      x: radius * Math.cos(angle),
      y: radius * Math.sin(angle),
      z: height * index / organs,
    });
  }
  return points;
};

cylindricalPhyllotaxis({organs: 100, radius: 7, height: 15})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="WbNwJQz" data-pen-title="Cylindrical phyllotaxis" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/WbNwJQz">
  Cylindrical phyllotaxis</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Wavy Patterns on the Z Axis

Adding oscillations to the cylindrical phyllotaxis model introduces a wave-like variation along the vertical axis, resulting in a pattern that appears dynamic and organic. The key to this effect lies in varying the radius of the cylinder as a function of the organ index. By introducing a sinusoidal term to the radius calculation, we create a repeating pattern of expansion and contraction along the z-axis. 

For example, a simple formula such as `radius + amplitude * Math.sin(index)` creates a smooth, oscillating effect. The `amplitude` controls the extent of these variations.

```javascript
/**
 * Cylindrical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.radius - The cylinder radius.
 * @param {number} options.height - The cylinder height.
 * @param {number} [options.amplitude=1] - Wave pattern amplitude. Defaults to 1.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const cylindricalPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), radius, height, amplitude = 1}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radiusConstant = radius + amplitude * Math.sin(index);
    points.push({
      x: radiusConstant * Math.cos(angle),
      y: radiusConstant * Math.sin(angle),
      z: height * index / organs,
    });
  }
  return points;
};

cylindricalPhyllotaxis({organs: 200, radius: 5, height: 20})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="VYwaxPK" data-pen-title="Cylindrical phyllotaxis - z waves" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/VYwaxPK">
  Cylindrical phyllotaxis - z waves</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

## The Conical Model

The conical model introduces a tapering effect to the cylindrical pattern, where the radius changes linearly from the base to the top.

The flexibility of this model comes from its two radius parameters: the base radius and the top radius. By manipulating these values, we can create diverse shapes:

- A cone if the top radius is zero.
- An inverted cone if the base radius is smaller than the top radius.
- A double cone if the base radius is greater than 0 and the top radius is lower than 0 (or viceversa).
- A cylinder if the base radius is equal to the top radius.

**Note:** The conical model often results in denser point distributions at the apex.

```javascript
/**
 * Conical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.baseRadius - The cone base radius.
 * @param {number} [options.topRadius=0] - The cone top radius.
 * @param {number} options.height - The cylinder height.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const conicalPhyllotaxis = ({organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5)), baseRadius, topRadius = 0, height}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const angle = index * divergenceAngle;
    const radiusConstant = baseRadius - (baseRadius - topRadius) * index / organs;
    points.push({
      x: radiusConstant * Math.cos(angle),
      y: radiusConstant * Math.sin(angle),
      z: height * index / organs, 
    });
  }
  return points;
};

conicalPhyllotaxis({organs: 200, baseRadius: 5, topRadius: 2, height: 20})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="PwozzwM" data-pen-title="Conical phyllotaxis" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/PwozzwM">
  Conical phyllotaxis</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

## The Spherical Model

The spherical model takes the concepts of the planar and cylindrical algorithms and maps them onto the surface of a sphere.

Points are calculated using spherical coordinates, with the divergence angle determining the rotation around the sphere and the elevation (latitude) set proportionally to the organ index. This creates an even, aesthetically pleasing distribution of points over the sphere’s surface.

```javascript
/**
 * Spherical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.radius - The sphere radius.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const sphericalPhyllotaxis = ({organs, divergenceAngle = 137.5, radius}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const phi = index * divergenceAngle;
    const theta = Math.acos(1 - 2 * (index / (organs - 1)));
    points.push({
      x: radius * Math.sin(theta) * Math.cos(phi * Math.PI / 180),
      y: radius * Math.sin(theta) * Math.sin(phi * Math.PI / 180),
      z: radius * Math.cos(theta),
    });
  }
  return points;
};

sphericalPhyllotaxis({organs: 200, radius: 7})
  .forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="xbxOOEz" data-pen-title="Spherical phyllotaxis" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/xbxOOEz">
  Spherical phyllotaxis</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Spherical Cap

The spherical cap variation of the spherical phyllotaxis model allows us to focus on a portion of the sphere, producing patterns that mimic natural structures such as flower heads, mushroom caps, or domed shapes. By restricting the z-axis range, we essentially "slice" the sphere, retaining only the topmost portion for the arrangement of points.

This is achieved by introducing a ratio parameter that determines how much of the sphere to include. A ratio of 1 uses the entire sphere, while a smaller value, such as 0.3, limits the pattern to a narrow cap.

```javascript
/**
 * Spherical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {number} options.radius - The sphere radius.
 * @param {number} [options.ratio=1] - The ratio of the sphere that will be used to generate (starting from the top). Defaults to 1.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const sphericalPhyllotaxis = ({organs, divergenceAngle = 137.5, radius, ratio = 1}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const phi = index * divergenceAngle;
    const theta = Math.acos(1 - 2 * (index / (organs - 1))) / (1 / ratio);
    points.push({
      x: radius * Math.sin(theta) * Math.cos(phi * Math.PI / 180),
      y: radius * Math.sin(theta) * Math.sin(phi * Math.PI / 180),
      z: radius * Math.cos(theta),
    });
  }
  return points;
};

sphericalPhyllotaxis({organs: 200, radius: 7, ratio: 0.3})
	.forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="mydEEON" data-pen-title="Spherical cap phyllotaxis" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/mydEEON">
  Spherical cap phyllotaxis</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Ellipsoids

Expanding on the spherical model, we can stretch or compress the sphere along its axes to form an ellipsoid. This transformation results in patterns that align with the diverse range of natural shapes, from elongated fruits like melons to flattened seed pods. The flexibility of the ellipsoid model lies in its ability to assign different radii for the x, y, and z axes.

By introducing separate parameters for each axis, the algorithm enables precise control over the pattern's proportions.

```javascript
/**
 * Spherical phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @param {object} options.radius - The sphere radius.
 * @param {number} options.radius.x - The sphere radius on the x axis.
 * @param {number} options.radius.y - The sphere radius on the y axis.
 * @param {number} options.radius.z - The sphere radius on the z axis.
 * @param {number} [options.ratio=1] - The ratio of the sphere that will be used to generate (starting from the top). Defaults to 1.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const sphericalPhyllotaxis = ({organs, divergenceAngle = 137.5, radius, ratio = 1}) => {
  const points = [];
  for (let index = 0; index < organs; index++) {
    const phi = index * divergenceAngle;
    const theta = Math.acos(1 - 2 * (index / (organs - 1))) / (1 / ratio);
    points.push({
      x: radius.x * Math.sin(theta) * Math.cos(phi * Math.PI / 180),
      y: radius.y * Math.sin(theta) * Math.sin(phi * Math.PI / 180),
      z: radius.z * Math.cos(theta),
    });
  }
  return points;
};

sphericalPhyllotaxis({organs: 200, radius: {x: 3, y: 5, z: 7}})
	.forEach(point => scene.add(makeSphere({radius: 1, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="bNGeeqp" data-pen-title="Spherical phyllotaxis - ellipsoid" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/bNGeeqp">
  Spherical phyllotaxis - ellipsoid</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

## The Surface of Revolution Model

In this model, a parametric curve—such as a Bézier curve—defines the profile of the shape to be revolved. The algorithm samples points along this curve and rotates them around a central axis using a specified divergence angle. This rotational mapping creates a surface of revolution, distributing points evenly across the generated shape.

The divergence angle ensures that the points are spaced in a manner reminiscent of natural growth patterns, similar to earlier phyllotaxis models. However, the addition of a curve allows for much greater control over the underlying geometry, enabling the creation of organic or abstract forms beyond the capabilities of planar, cylindrical, or spherical models.

Unlike earlier models that relied solely on mathematical formulas, this algorithm requires the use of a library capable of representing curves and sampling points along them. Additionally, it incorporates vector mathematics to manipulate points in three-dimensional space.

```javascript
/**
 * Surface of revolution phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {object} options.curve - The Besier curve used to generate the surface of revolution.
 * @param {number} options.organs - The number of organs in the arrangement.
 * @param {number} [options.divergenceAngle=Math.PI * (3 - Math.sqrt(5))] - The divergence angle between organs (in radians). Defaults to the golden angle.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const surfaceOfRevolutionPhyllotaxis = ({curve, organs, divergenceAngle = Math.PI * (3 - Math.sqrt(5))}) => {
  return curve.getSpacedPoints(organs).map((point, index) => point.applyAxisAngle(new THREE.Vector3(0, 0, 1), index * divergenceAngle));
};

const curve = new THREE.QuadraticBezierCurve3(
	new THREE.Vector3(10, 0, 0),
	new THREE.Vector3(20, 0, 20),
	new THREE.Vector3(10, 0, 20)
);

surfaceOfRevolutionPhyllotaxis({curve: curve, organs: 300})
  .forEach(point => scene.add(makeSphere({radius: 1.5, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="LEYZZWX" data-pen-title="Surface of revolution phyllotaxis" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/LEYZZWX">
  Surface of revolution phyllotaxis</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Compact Patterns

The compact variant of the surface of revolution phyllotaxis takes a more dynamic approach to distributing organs on the surface of a revolution. Instead of specifying a fixed number of organs, this variant allows users to define the size of each organ (e.g., the radius of a sphere encompassing the organ). The algorithm then determines the placement of organs based on the available surface area, ensuring efficient packing and realistic spacing.

```javascript
/**
 * Surface of revolution phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {object} options.curve - The Besier curve used to generate the surface of revolution.
 * @param {number} options.organSize - The radius of the sphere encompasing the organ.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const surfaceOfRevolutionPhyllotaxis = ({curve, organSize}) => {
  const points = [];
  const divergenceAngle = Math.PI * (3 - Math.sqrt(5));
  const curveLength = curve.getLength();
  const deltaS = 0.001;
  
  let arcLength = 0;
  let area = 0;
  let index = 0;
  let position;

  while (arcLength < curveLength) {
    while (area < 1 && arcLength < curveLength) {
      // this is a hackish way of getting a point that's at a specific distance away from the start point
      // curve.getPoint(..) expects a value in the [0, 1] interval, but the algorithm uses a value in the [0, curve.getLength()] interval
      position = curve.getPoint(curve.getUtoTmapping(null, arcLength));
      area += ((2 * position.x) / Math.pow(organSize, 2)) * deltaS;
      arcLength += deltaS;
    }
    area -= 1;
    const rotated = position.applyAxisAngle(new THREE.Vector3(0, 0, 1), index * divergenceAngle);
    points.push(rotated);
    index++;
  }
  
  return points;
};

const curve = new THREE.QuadraticBezierCurve3(
	new THREE.Vector3(10, 0, 0),
	new THREE.Vector3(20, 0, 20),
	new THREE.Vector3(10, 0, 20)
);

surfaceOfRevolutionPhyllotaxis({curve: curve, organSize: 1.5})
  .forEach(point => scene.add(makeSphere({radius: 1.5, center: point})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="XJWKKgE" data-pen-title="Surface of revolution phyllotaxis - compact" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/XJWKKgE">
  Surface of revolution phyllotaxis - compact</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

### Compact Patterns with Dynamic Organ Size

Building on the previous example, this variant introduces support for dynamic radii, allowing the size of each organ to change as it is placed. By specifying a starting organ size as well as an ending organ size, this variant provides flexibility for simulating a wide range of natural patterns and artistic designs.

The provided implementations uses linear interpolation to choose radius values in the start organ size - end organ size interval. As an alternative, other types of easing functions could be used. 

```javascript
/**
 * Surface of revolution phyllotaxis algorithm.
 * 
 * @param {object} options
 * @param {object} options.curve - The Besier curve used to generate the surface of revolution.
 * @param {number} options.startOrganSize - The initial radius used for the sphere encompasing the organ.
 * @param {number} options.endOrganSize - The final radius used for the sphere encompasing the organ.
 * @return {Object[]} - An array of 3D points representing the phyllotaxis arrangement.
 */
const surfaceOfRevolutionPhyllotaxis = ({curve, startOrganSize, endOrganSize}) => {
  const organs = [];

  const divergenceAngle = Math.PI * (3 - Math.sqrt(5))
  const curveLength = curve.getLength();
  const deltaS = 0.001;
  
  let arcLength = 0;
  let area = 0;
  let index = 0;
  let position;
  let organSize = 0;

  while (arcLength < curveLength) {
    while (area < 1 && arcLength < curveLength) {
      position = curve.getPoint(curve.getUtoTmapping(null, arcLength));
      organSize = THREE.MathUtils.lerp(startOrganSize, endOrganSize, arcLength / curveLength);
      area += ((2 * position.x) / Math.pow(organSize, 2)) * deltaS;
      arcLength += deltaS;
    }
    area -= 1;
    const rotated = position.applyAxisAngle(new THREE.Vector3(0, 0, 1), index * divergenceAngle);
    organs.push({position: rotated, radius: organSize});
    index++;
  }
  
  return organs;
};

const curve = new THREE.QuadraticBezierCurve3(
	new THREE.Vector3(10, 0, 0),
	new THREE.Vector3(20, 0, 20),
	new THREE.Vector3(10, 0, 20)
);

surfaceOfRevolutionPhyllotaxis({curve: curve, startOrganSize: 1, endOrganSize: 3})
  .forEach(organ => scene.add(makeSphere({radius: organ.radius, center: organ.position})));
```

<p class="codepen" data-height="550" data-default-tab="result" data-slug-hash="gbOMMGb" data-pen-title="Surface of revolution phyllotaxis - compact pattern, dynamic radius" data-preview="true" data-editable="true" data-user="surubel" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/surubel/pen/gbOMMGb">
  Surface of revolution phyllotaxis - compact pattern, dynamic radius</a> by Alexandru Chirita (<a href="https://codepen.io/surubel">@surubel</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>


# Resources
- [Algorithmic Botany - Chapter 4](https://algorithmicbotany.org/papers/abop/abop-ch4.pdf)
- [Algorithmic Botany - The use of positional information in the modeling of plants](https://algorithmicbotany.org/papers/sigcourse.2003/2-27-positional.pdf)