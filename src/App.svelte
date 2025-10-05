<script>
  import Icon from "@iconify/svelte";
  import { TrashBinOutline } from "flowbite-svelte-icons";

  import {
    Card,
    Img,
    CloseButton,
    Button,
    Rating,
    Badge,
  } from "flowbite-svelte";

  // Déclarer products en tableau vide evolutif
  let products = $state([]);
  // Fonctio qui récupère les info de l'api et stockage dans products
  const getProducts = async () => {
    const response = await fetch(
      "http://127.0.0.1:8090/api/collections/products/records"
    );
    const responseData = await response.json();
    products = responseData.items;
  };
  getProducts();
  // Pour le if ouverture du panier
  let click = $state(false);
  const isClicked = () => {
    click = !click;
  };
  // Déclaration du panier de l'utilisateur dans le localStorage sous forme de tableau vide
  let userCart = $state(JSON.parse(localStorage.getItem("cart") || "[]"));

  // let whatId = $state(null);

  //Fonction pour le bouton "Ajouter au panier"
  const addToCart = (id) => {
    let quantity = 1;
    const findCart = userCart.find((x) => x.id === id);
    const findProducts = products.find((c) => c.id === id);
    if (findCart) {
      findCart.quantity++;
      localStorage.setItem("cart", JSON.stringify(userCart));
    } else
      userCart.push({
        id: findProducts.id,
        title: findProducts.title,
        image: findProducts.image,
        price: findProducts.price,
        quantity: (findProducts.quantity = quantity++),
      });
    localStorage.setItem("cart", JSON.stringify(userCart));
  };

  // Fonction pour modifier la quantité + supprimé du panier si la quantité est égal à 0
  const modifyUserArticleForAdd = (nextId) => {
    const findArticleToModify = userCart.find(
      (article) => article.id === nextId
    );
    if (findArticleToModify) {
      findArticleToModify.quantity++;
      localStorage.setItem("cart", JSON.stringify(userCart));
    }
  };
  const modifyUserArticleForDecrease = (backId) => {
    const findArticleToDecrease = userCart.find((a) => a.id === backId);
    const index = userCart.findIndex((a) => a.id === backId);

    if (findArticleToDecrease) {
      findArticleToDecrease.quantity--;
      localStorage.setItem("cart", JSON.stringify(userCart));
    }
    if (findArticleToDecrease.quantity === 0) {
      userCart.splice(index, 1);
      localStorage.setItem("cart", JSON.stringify(userCart));
    }
    console.log(findArticleToDecrease);
  };
  // Supprimer l'article
  const deleteUserArticle = (deleteId) => {
    const findArticleToDelete = userCart.find((d) => d.id === deleteId);
    const findIdToDelete = userCart.findIndex((d) => d.id === deleteId);
    if (findArticleToDelete) {
      userCart.splice(findIdToDelete, 1);
      localStorage.setItem("cart", JSON.stringify(userCart));
    }
  };
  // Prix total

  const total = $derived(
    userCart.reduce((acc, curr) => acc + curr.quantity * curr.price, 0)
  );
</script>

<header class=" w-auto shadow-md mb-4">
  <nav class="flex justify-between items-center h-[4rem]">
    <h1 class="text-2xl font-bold text-primary-900">
      Fast<span class=" text-primary-500"> Shop</span>
    </h1>
    <div class="flex">
      <Icon
        class="text-primary-500 cursor-pointer"
        id="cartLogo"
        icon="mdi:cart"
        width="24"
        height="24"
        onclick={() => isClicked()}
      />
      <p class="font-bold text-primary-900">{total.toFixed(2)}</p>
    </div>
  </nav>
</header>
{#if click === true}
  <section class="inset-0 fixed z-50 flex justify-center items-center">
    <div class="p-4 bg-primary-50">
      <CloseButton
        class="w-full flex flex-row-reverse"
        onclick={() => (click = false)}
      />
      <div class="flex gap-1">
        <Icon
          class="text-primary-500"
          icon="material-symbols:shopping-bag-outline"
          width="24"
          height="24"
        />
        <h2 class="font-bold text-primary-900">Votre panier</h2>
        <p class="font-bold text-primary-900">({userCart.length} articles)</p>
      </div>

      <div class="max-h-60 overflow-y-auto">
        {#each userCart as course}
          <article class="">
            <Card
              class="size-fit flex flex-row justify-center items-center gap-4"
            >
              <Img
                src={course.image}
                class="size-16 object-cover "
                alt={course.title}
              />
              <p
                class="text-primary-900 font-medium overflow-hidden whitespace-nowrap text-ellipsis w-full"
              >
                {course.title}
              </p>
              <p class="text-primary-900 font-medium">{course.price}€</p>

              <div class="flex flex-row justify-center items-center">
                <button
                  class="text-primary-900 font-medium"
                  onclick={() => modifyUserArticleForAdd(course.id)}>+</button
                >
                <p class="m-3 text-primary-900 font-medium">
                  {course.quantity}
                </p>
                <button
                  class="text-primary-900 font-medium"
                  onclick={() => modifyUserArticleForDecrease(course.id)}
                  >-</button
                >
              </div>
              <button
                class="delete__cart"
                onclick={() => deleteUserArticle(course.id)}
                ><TrashBinOutline
                  size="lg"
                  class="size-20 text-red-500 "
                /></button
              >
            </Card>
          </article>
        {/each}
      </div>
      <h4>Total : <span class="total__price">{total.toFixed(2)}</span></h4>
    </div>
  </section>
{/if}
<main class="p-4">
  <section class="grid grid-cols-2 gap-2">
    {#each products as product}
      <Card class="p-0 ">
        <img
          src={product.image}
          alt={product.title}
          class="rounded-t-2xl object-cover w-full aspect-square"
        />
        <div class="p-1">
          <h3 class="text-primary-900 font-bold text-left">
            {product.title}
          </h3>
          <p class="text-sm text-left text-gray-500">
            {product.description}
          </p>
          <div class="flex justify-between items-center p-1">
            <p class="price__card font-bold text-primary-500">
              {product.price}€
            </p>
            <button
              class="bg-primary-500 rounded-xl"
              id="addToCart"
              onclick={() => addToCart(product.id)}
            >
              <p class="text-xm text-amber-50 font-bold m-2">Ajouter</p>
            </button>
          </div>
        </div>
      </Card>
    {/each}
  </section>
</main>
