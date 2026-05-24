<!-- app/components/MorphScene.client.vue -->

<template>
  <TresGroup v-if="isReady">
    
    <TresPoints>
      
      <TresBufferGeometry ref="geometryRef" />

      <!-- Partículas -->
      <TresPointsMaterial
        :size="0.04"

        color="#7b2cff"

        :transparent="true"

        :opacity="0.95"

        :size-attenuation="true"

        :blending="THREE.AdditiveBlending"
      />

    </TresPoints>

  </TresGroup>

  <!-- Luces -->
  <TresAmbientLight :intensity="1.5" />

  <TresDirectionalLight
    :position="[5, 5, 5]"
    :intensity="1"
  />

</template>

<script setup>

import * as THREE from 'three'

import { STLLoader }
from 'three/examples/jsm/loaders/STLLoader'

import { MeshSurfaceSampler }
from 'three/examples/jsm/math/MeshSurfaceSampler'

import { gsap } from 'gsap'

import { ScrollTrigger }
from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

/* Referencia geometría */
const geometryRef = ref(null)

/* Estado */
const isReady = ref(false)

/* Cantidad partículas */
const particleCount = 20000

/* Posiciones */
const currentPositions =
new Float32Array(particleCount * 3)

/* Cargar modelos */
const loadModels = async () => {

  const loader = new STLLoader()

  const urls = [
    '/models/1.stl',
    '/models/2.stl',
    '/models/3.stl',
    '/models/4.stl',
    '/models/5.stl'
  ]

  try {

    const geometriesData =
    await Promise.all(

      urls.map(async (url) => {

        /* Cargar STL */
        const geo =
        await loader.loadAsync(url)

        /* Rotar */
        geo.rotateX(-Math.PI / 2)

        /* Centrar */
        geo.center()

        /* Escala automática */
        geo.computeBoundingSphere()

        if (geo.boundingSphere.radius > 0) {

          const scale =
          3 / geo.boundingSphere.radius

          geo.scale(scale, scale, scale)
        }

        /* Mesh temporal */
        const tempMesh =
        new THREE.Mesh(geo)

        /* Sampler */
        const sampler =
        new MeshSurfaceSampler(tempMesh).build()

        /* Array partículas */
        const sampledPoints =
        new Float32Array(particleCount * 3)

        const tempVec =
        new THREE.Vector3()

        /* Muestreo */
        for (let i = 0; i < particleCount; i++) {

          sampler.sample(tempVec)

          sampledPoints[i * 3] = tempVec.x
          sampledPoints[i * 3 + 1] = tempVec.y
          sampledPoints[i * 3 + 2] = tempVec.z
        }

        return sampledPoints
      })
    )

    /* Primera geometría */
    currentPositions.set(geometriesData[0])

    isReady.value = true

    nextTick(() => {

      if (geometryRef.value) {

        geometryRef.value.setAttribute(
          'position',

          new THREE.BufferAttribute(
            currentPositions,
            3
          )
        )

        setupScrollAnimation(geometriesData)
      }
    })

  } catch (err) {

    console.error(
      'Error cargando STL:',
      err
    )
  }
}

/* Scroll animation */
const setupScrollAnimation =
(geometriesData) => {

  const tl = gsap.timeline({

    scrollTrigger: {

      trigger: '#scroll-wrapper',

      start: 'top top',

      end: 'bottom bottom',

      scrub: 1.5,
    }
  })

  /* Morphing */
  for (
    let i = 0;
    i < geometriesData.length - 1;
    i++
  ) {

    tl.to(currentPositions, {

      endArray: geometriesData[i + 1],

      duration: 1,

      ease: 'power2.inOut',

      onUpdate: () => {

        if (geometryRef.value) {

          geometryRef.value
            .attributes
            .position
            .needsUpdate = true
        }
      }
    })
  }
}

/* Inicializar */
onMounted(() => {

  loadModels()
})

</script>