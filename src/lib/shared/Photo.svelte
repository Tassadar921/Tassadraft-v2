<script>
  import { Camera, CameraResultType, CameraSource } from '@capacitor/camera';
  import Fab from './Fab.svelte';
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  const takePhoto = async () => {
    const photo = await Camera.getPhoto({
      resultType: CameraResultType.Uri,
      source: CameraSource.Camera,
      quality: 90,
    });

    dispatch('photo', { photo });
  };
</script>

<Fab icon="camera" horizontal="middle" vertical="bottom" on:click={takePhoto} />
