<!-- APP.VUE -->

<template>
  <main>
    <!-- Canvas fijo -->
    <div class="fixed-canvas">
      <ClientOnly>
        <TresCanvas window-size clear-color="#050505">
          
          <!-- Cámara -->
          <TresPerspectiveCamera :position="[0, 0, 8]" />

          <!-- Controles -->
          <OrbitControls />

          <!-- Escena 3D -->
          <MorphScene />

          <!-- Luz ambiental -->
          <TresAmbientLight :intensity="0.5" />
        </TresCanvas>

        <!-- Loader -->
        <template #fallback>
          <div class="loader">Cargando modelos 3D...</div>
        </template>
      </ClientOnly>
    </div>

    <!-- Scroll -->
    <div id="scroll-wrapper">
      <section
        v-for="n in 5"
        :key="n"
        class="step-section"
      >
        <div class="content-card">
          <span>0{{ n }}</span>

          <h2>Fase de Desintegración</h2>

          <p>
            Transformación dinámica de modelos STL
            mediante partículas 3D y animaciones
            controladas con scroll.
          </p>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped>

body {
  margin: 0;
  overflow-x: hidden;
  background: #050505;
}

/* Canvas fijo */
.fixed-canvas {
  position: fixed;
  inset: 0;
  z-index: 0;
}

/* Scroll largo */
#scroll-wrapper {
  height: 800vh;
  position: relative;
  z-index: 10;
}

/* Secciones */
.step-section {
  height: 200vh;
  display: flex;
  align-items: center;
  padding-left: 10%;
}

/* Tarjeta */
.content-card {
  background: rgba(255, 255, 255, 0.05);

  backdrop-filter: blur(10px);

  padding: 2rem;

  border-left: 3px solid #00aaff;

  border-radius: 20px;

  color: white;

  max-width: 420px;

  box-shadow:
    0 0 20px rgba(0, 170, 255, 0.3),
    0 0 50px rgba(123, 44, 255, 0.2);

  transition: 0.3s ease;
}

.content-card:hover {
  transform: translateY(-5px);

  box-shadow:
    0 0 30px rgba(0, 170, 255, 0.6),
    0 0 60px rgba(123, 44, 255, 0.4);
}

.content-card span {
  font-size: 5rem;
  font-weight: bold;
  color: #7b2cff;
  opacity: 0.3;
}

.content-card h2 {
  margin-top: -20px;
  font-size: 2rem;
  color: #ffffff;
}

.content-card p {
  color: #cfcfcf;
  line-height: 1.6;
}

/* Loader */
.loader {
  color: #7b2cff;

  display: flex;

  justify-content: center;

  align-items: center;

  height: 100vh;

  font-size: 1.5rem;

  font-family: monospace;

  text-shadow:
    0 0 10px #7b2cff,
    0 0 30px #00aaff;
}

</style>