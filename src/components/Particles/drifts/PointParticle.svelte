<script lang="ts">
  import { T, useTask } from '@threlte/core'
  import { useTexture } from '@threlte/extras'
  import * as THREE from 'three'

  let { position, png, turboColor } = $props();

  const texture = useTexture(png);
  let materialRef = $state<THREE.PointsMaterial>();
  let size = $state(0);
  let opacity = $state(1);

  const geometry = new THREE.BufferGeometry();
  geometry.setAttribute('position', new THREE.Float32BufferAttribute(position, 3));

  $effect(() => {
    if (materialRef) {
      materialRef.color.multiplyScalar(10);
    }
    size = 0;
    opacity = 1;
  });

  useTask((delta) => {
    if (turboColor === 0xffffff) return;
    if (size < 5) {
      size = Math.min(size + 0.3 * delta * 144, 5);
    } else if (opacity > 0) {
      opacity = Math.max(opacity - 0.2 * delta * 144, 0);
      size = Math.max(size - 0.2 * delta * 144, 0);
    }
  });
</script>

{#if $texture}
    <T.Points {geometry}>
        <T.PointsMaterial
            bind:ref={materialRef}
            {size}
            alphaMap={$texture}
            transparent={true}
            depthWrite={false}
            color={turboColor}
            {opacity}
            toneMapped={false}
        />
    </T.Points>
{/if}
