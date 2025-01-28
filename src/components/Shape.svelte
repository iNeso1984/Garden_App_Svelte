<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  export let shape; // Shape or icon data passed as a prop
  export let onDeleteShape; // Callback to handle shape deletion

  let svg;

  // Draw the shape or icon and enable interactions
  onMount(() => {
    drawShape();
    enableInteractions();
  });

  function drawShape() {
    const svgContainer = d3.select(svg);
    svgContainer.selectAll('*').remove(); // Clear previous content

    if (shape.type === 'rectangle') {
      const rect = svgContainer
        .append('rect')
        .attr('x', shape.position.x)
        .attr('y', shape.position.y)
        .attr('width', shape.width)
        .attr('height', shape.height)
        .attr('fill', shape.color)
        .attr('stroke', '#444')
        .attr('stroke-width', 2);

      // Make rectangle draggable
      rect.call(d3.drag().on('drag', (event) => {
        shape.position.x += event.dx;
        shape.position.y += event.dy;
        drawShape();
      }));

      // Add resize handle for the rectangle
      svgContainer
        .append('rect')
        .attr('x', shape.position.x + shape.width - 10)
        .attr('y', shape.position.y + shape.height - 10)
        .attr('width', 10)
        .attr('height', 10)
        .attr('fill', 'black')
        .style('cursor', 'se-resize')
        .call(d3.drag().on('drag', (event) => {
          shape.width = Math.max(20, shape.width + event.dx);
          shape.height = Math.max(20, shape.height + event.dy);
          drawShape();
        }));
    } else if (shape.type === 'circle') {
      const circle = svgContainer
        .append('circle')
        .attr('cx', shape.position.x + shape.width / 2)
        .attr('cy', shape.position.y + shape.height / 2)
        .attr('r', shape.width / 2)
        .attr('fill', shape.color)
        .attr('stroke', '#444')
        .attr('stroke-width', 2);

      // Make circle draggable
      circle.call(d3.drag().on('drag', (event) => {
        shape.position.x += event.dx;
        shape.position.y += event.dy;
        drawShape();
      }));

      // Add resize handle for the circle
      svgContainer
        .append('circle')
        .attr('cx', shape.position.x + shape.width)
        .attr('cy', shape.position.y + shape.height)
        .attr('r', 5)
        .attr('fill', 'black')
        .style('cursor', 'se-resize')
        .call(d3.drag().on('drag', (event) => {
          const dx = event.x - shape.position.x;
          const dy = event.y - shape.position.y;
          const radius = Math.max(10, Math.sqrt(dx * dx + dy * dy));
          shape.width = radius * 2;
          shape.height = radius * 2;
          drawShape();
        }));
    } else if (shape.type === 'icon') {
      // Draw icon
      svgContainer
        .append('image')
        .attr('xlink:href', shape.icon)
        .attr('x', shape.position.x)
        .attr('y', shape.position.y)
        .attr('width', shape.width)
        .attr('height', shape.height)
        .call(d3.drag().on('drag', (event) => {
          shape.position.x += event.dx;
          shape.position.y += event.dy;
          drawShape();
        }));

      // Add resize handle for the icon
      svgContainer
        .append('rect')
        .attr('x', shape.position.x + shape.width - 10)
        .attr('y', shape.position.y + shape.height - 10)
        .attr('width', 10)
        .attr('height', 10)
        .attr('fill', 'black')
        .style('cursor', 'se-resize')
        .call(d3.drag().on('drag', (event) => {
          shape.width = Math.max(20, shape.width + event.dx);
          shape.height = Math.max(20, shape.height + event.dy);
          drawShape();
        }));
    }

    // Add labels for shapes
    shape.labels.forEach((label, index) => {
      svgContainer
        .append('text')
        .attr('x', shape.position.x + shape.width / 2)
        .attr('y', shape.position.y - 10 - index * 20)
        .attr('text-anchor', 'middle')
        .attr('fill', 'black')
        .text(label);
    });
  }

  function enableInteractions() {
    drawShape();
  }
</script>

<svg bind:this={svg} class="shape-canvas" width="100%" height="100%"></svg>

<!-- Delete button -->
<div class="delete-button-container">
  <button class="delete-btn" on:click={onDeleteShape}>x</button>
</div>

<style>
  .shape-canvas {
    position: absolute;
    pointer-events: all;
  }
  .delete-button-container {
    position: absolute;
    top: -15px;
    right: -15px;
  }
  .delete-btn {
    width: 20px;
    height: 20px;
    border: none;
    background: red;
    color: white;
    border-radius: 50%;
    font-size: 12px;
    cursor: pointer;
  }
  .delete-btn:hover {
    background: darkred;
  }
</style>
