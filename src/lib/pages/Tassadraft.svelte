<script>
  import Menu from '../menu/Menu.svelte';
  import Photo from '../shared/Photo.svelte';
  import TassadraftSegment from '../tassadraft/TassadraftSegment.svelte';
  import Title from '../shared/Title.svelte';
  import PhotosList from '../shared/PhotosList.svelte';
  import CardsTable from '../tables/card/CardsTable.svelte';

  let photos = [];
  let cards = [];
  let state = 'Photos';
  let deletedPhotos = [];
  let deletedCards = [];

  const handleProcessed = (e) => {
    const unprocessedPhotos = photos.filter((photo) => !photo.processed);
    e.detail?.cards?.forEach((cardObject) => {
      let savedCard = cards.find(
        (c) => c.scryfallId === cardObject.card.scryfallId,
      );
      if (!savedCard) {
        savedCard = { ...cardObject.card, photos: [] };
      }
      unprocessedPhotos.forEach((photo, index) => {
        if (cardObject.images.includes(index)) {
          photo.cards = [...photo.cards, savedCard];
          savedCard.photos = [...savedCard.photos, photo.uri];
        }
      });
      if (!cards.includes(savedCard)) {
        cards = [...cards, savedCard];
      }
    });

    photos = photos.map((photo) => ({ ...photo, processed: true }));
  };
</script>

<Menu />
<Title title="Tassadraft" />
<TassadraftSegment
  bind:selectedOption={state}
  bind:photos
  on:processed={handleProcessed}
/>

{#if state === 'Photos'}
  <PhotosList bind:deletedPhotos bind:cards bind:photos />
  <Photo
    on:photo={(e) =>
      (photos = [
        ...photos,
        { uri: e.detail.photo.webPath, cards: [], processed: false },
      ])}
  />
{:else if state === 'Cards'}
  <CardsTable bind:deletedCards bind:cards />
{/if}
