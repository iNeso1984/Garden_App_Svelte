  <script>
  import Shape from './components/Shape.svelte';
  import { writable } from 'svelte/store';
  import html2canvas from 'html2canvas';

  // Shapes and controls
  let shapes = writable([]);
  let shapeType = 'rectangle';
  let color = '#4d7358';
  let label = '';

  // List of vegetables, fruits, flowers, and herbs:
  let icons = [
    { id: 'icon-1', src: 'https://img.icons8.com/?size=100&id=20110&format=png&color=000000', name: 'Lettuce1' },
    { id: 'icon-2', src: 'https://img.icons8.com/?size=100&id=1aaomP3K4Jt5&format=png&color=000000', name: 'Tomato' },
    { id: 'icon-3', src: 'https://img.icons8.com/?size=100&id=20874&format=png&color=000000', name: 'Eggplant' },
    { id: 'icon-4', src: 'https://img.icons8.com/?size=100&id=aa5wKFNhpW3A&format=png&color=000000', name: 'Spinach' },
    { id: 'icon-5', src: 'https://img.icons8.com/?size=100&id=D6mAH6vTkBH4&format=png&color=000000', name: 'Strawberry' },
    { id: 'icon-6', src: 'https://img.icons8.com/?size=100&id=opsdrx5KGftm&format=png&color=000000', name: 'Red Cabbage' },
    { id: 'icon-7', src: 'https://img.icons8.com/?size=100&id=XdVNldKEASds&format=png&color=000000', name: 'Yellow Squash' },
    { id: 'icon-8', src: 'https://img.icons8.com/?size=100&id=ENhBaKOIvAoo&format=png&color=000000', name: 'Onions' },
    { id: 'icon-9', src: 'https://img.icons8.com/?size=100&id=MpV4pRC3Oqx5&format=png&color=000000', name: 'Green Onions' }
  ];

  // Add a new shape
  const addShape = () => {
    const newShape = {
      id: Math.random().toString(36).substr(2, 9),
      type: shapeType,
      position: { x: Math.random() * 900, y: Math.random() * 600 },
      labels: [label],
      width: shapeType === 'rectangle' ? 200 : 100,
      height: shapeType === 'rectangle' ? 100 : 100,
      color,
    };
    shapes.update((currentShapes) => [...currentShapes, newShape]);
    label = ''; // Reset label after adding shape
  };

  // Handle drag and drop for icons
  const handleIconDragStart = (event, icon) => {
    event.dataTransfer.setData('icon', JSON.stringify(icon));
  };

  const handleDrop = (event) => {
    const iconData = JSON.parse(event.dataTransfer.getData('icon'));
    const newShape = {
      id: Math.random().toString(36).substr(2, 9),
      type: 'icon',
      position: { x: event.offsetX, y: event.offsetY },
      labels: [iconData.name],
      width: 40,
      height: 40,
      icon: iconData.src,
    };
    shapes.update((currentShapes) => [...currentShapes, newShape]);
  };

  // Delete a shape
  const deleteShape = (id) => {
    shapes.update((currentShapes) => currentShapes.filter((shape) => shape.id !== id));
  };

  // Save canvas as an image
  const saveCanvasAsImage = () => {
    const canvas = document.getElementById('canvas-container');
    html2canvas(canvas).then((canvasElement) => {
      const link = document.createElement('a');
      link.href = canvasElement.toDataURL();
      link.download = 'garden-canvas.png';
      link.click();
    });
  };
</script>

<main on:drop={handleDrop} on:dragover|preventDefault>
  <h1 class="app-title">My Garden App</h1>

  <div class="controls">
    <div class="control-section">
      <h2>Ground, Bed, & Container Shape</h2>
      <div class="control-item">
        <label for="shape">Shape Type:</label>
        <select id="shape" bind:value={shapeType}>
          <option value="rectangle">Rectangle</option>
          <option value="circle">Circle</option>
        </select>
      </div>
      <div class="control-item">
        <label for="color">Choose Shape Color:</label>
        <input type="color" id="color" bind:value={color} />
      </div>
      <div class="control-item">
        <label for="label">Label Shape:</label>
        <input type="text" id="label" bind:value={label} placeholder="Enter label here" />
      </div>
      <div class="control-item">
        <button on:click={addShape}>Add Shape</button>
      </div>
    </div>
  </div>

  <div>
    <h4>Drag and drop vegetables into your garden:</h4>
  </div>

  <div class="icons">
    {#each icons as icon}
      <img
        src={icon.src}
        alt={icon.name}
        draggable="true"
        on:dragstart={(e) => handleIconDragStart(e, icon)}
        class="icon"
      />
    {/each}
  </div>

  <div id="canvas-container" class="canvas">
    {#each $shapes as shape (shape.id)}
      <Shape {shape} on:deleteShape={() => deleteShape(shape.id)} />
    {/each}
  </div>

  <div class="save-button">
    <button on:click={saveCanvasAsImage}>Save Image As</button>
  </div>

</main>
<footer>
  <p>© Vanessa Wwithun</p>
  <p><a href="vanessawithun.com">vanessawithun.com</a></p>
<div>

</div>
</footer>



<style>
 .controls {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-bottom: 20px;
  
}
  .control-section {
    flex: 1;
    min-width: 300px;
    background: rgb(31, 31, 31);
    padding: 16px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }
  .control-item {
    display: flex;
    flex-direction: column;
    gap: 4px;
    margin-bottom: 12px;
  }
  
  label {
    font-size: 0.9rem;
    font-weight: 600;
    margin-bottom: 4px;
    color: white;
  }
  input, select {
    padding: 6px 8px;
    font-size: 0.9rem;
    color: white;
    border-radius: 4px;
    border: 1px solid white;
    width: 100%;
  }
  button {
    padding: 8px 12px;
    background-color: rgb(81, 138, 108);
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background-color 0.3s;
    margin-bottom: 20px;
  }
  button:hover {
    background-color: rgb(138, 200, 141);
  }
  .icons {
    display: flex;
    gap: 10px;
    margin-bottom: 16px;
  }
  .icon {
    width: 42px;
    height: 42px;
    cursor: grab;
    border-radius: 4px;
    border: 1px solid #ccc;
  }
 .canvas {
  position: relative;
  width: 100%; /* Make it responsive */
  max-width: 1024px; /* Optional: Restrict max width */
  height: 768px;
  border: 2px solid #ccc;
  background-color: rgb(158, 154, 151);
  overflow: visible; /* Prevent clipping of shapes */
  margin: 20px auto; /* Center the canvas and add spacing */
}
  .save-button {
    margin-top:20px;
  
    text-align: center; /* Center the button horizontally */
  }

  .save-button button {
    /* Inherit button styles from your existing styles */
    /* Or define specific styles for the save button */
    padding: 10px 20px; 
    background-color: #4CAF50; 
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
   
  }
footer {
  box-sizing: border-box;
  width: 100%;
  background: #141414;
  display:grid;
  grid-template-columns: 1fr 1fr 1fr;
  padding-bottom:30px;
}
</style>
