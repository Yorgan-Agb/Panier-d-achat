<script>
  import Icon from "@iconify/svelte";
  import {
    TrashBinOutline,
    ShoppingBagSolid,
    CartSolid,
  } from "flowbite-svelte-icons";
  // import { ShoppingBagSolid } from "flowbite-svelte-icons";

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
    <h1 class="text-2xl font-bold text-primary-900 lg:text-4xl">
      Fast<span class=" text-primary-500"> Shop</span>
    </h1>
    <div class="flex">
      <CartSolid
        size="xl"
        class="text-primary-500 cursor-pointer "
        onclick={() => isClicked()}
      />

      <p class="font-bold text-primary-900 lg:text-xl">
        {total.toFixed(2)} €
      </p>
    </div>
  </nav>
</header>
{#if click === true}
  <div class="fixed inset-0 bg-black/50 z-40"></div>
  <section class="inset-0 fixed z-50 flex justify-center items-center">
    <div class=" w-[95%] bg-white rounded-2xl sm:w-sm lg:w-[80%]">
      <div class="p-4">
        <CloseButton
          class="w-full flex flex-row-reverse "
          onclick={() => (click = false)}
        />
        <div class="flex gap-1 mb-5">
          <ShoppingBagSolid class="text-primary-500 lg:h-8 w-8" />
          <h2 class="font-bold text-primary-900 sm:text-lg lg:text-2xl">
            Votre panier
          </h2>
          <p class="font-bold text-primary-900 sm:text-lg lg:text-2xl">
            ({userCart.length} articles)
          </p>
        </div>

        <div class="max-h-60 overflow-y-auto">
          {#each userCart as course}
            <article class="m-2">
              <Card
                class="size-fit flex flex-row justify-center items-center gap-4 bg-primary-50 border-none md-sm: w-full max-w-full"
              >
                <Img
                  src={course.image}
                  class="size-16 object-cover sm:w-sm"
                  alt={course.title}
                />
                <p
                  class="text-primary-900 font-medium overflow-hidden whitespace-nowrap text-ellipsis w-full lg:text-xl"
                >
                  {course.title}
                </p>
                <p class="text-primary-900 font-medium lg:text-xl">
                  {course.price}€
                </p>

                <div class="flex flex-row justify-center items-center">
                  <button
                    class="text-primary-900 font-medium lg:text-xl"
                    onclick={() => modifyUserArticleForAdd(course.id)}>+</button
                  >
                  <p class="m-3 text-primary-900 font-medium lg:text-xl">
                    {course.quantity}
                  </p>
                  <button
                    class="text-primary-900 font-medium lg:text-xl"
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
        <div class="flex justify-between mt-5 mb-2">
          <p class="font-bold lg:text-xl">Total :</p>
          <p class="text-primary-500 font-bold lg:text-xl">
            {total.toFixed(2)}€
          </p>
        </div>
        <div class="flex flex-col items-center gap-2 mb-5">
          <button
            class=" w-full max-w-96 h-8 border-1 border-solid rounded-sm border-gray-200"
            onclick={() => (click = false)}
          >
            <p class="font-medium lg:text-xl">Continuer mes achats</p>
          </button>
          <button class="bg-primary-500 w-full max-w-96 h-8 rounded-sm"
            ><p class="font-medium text-amber-50 lg:text-xl">
              Procéder au paiement
            </p></button
          >
        </div>
      </div>
    </div>
  </section>
{/if}
<main class="p-4">
  <section class="grid grid-cols-2 gap-2 lg:grid-cols-3">
    {#each products as product}
      <Card class="p-0 ">
        <img
          src={product.image}
          alt={product.title}
          class="rounded-t-2xl object-cover w-full aspect-square"
        />
        <div class="p-1">
          <h3 class="text-primary-900 font-bold text-left lg:text-lg">
            {product.title}
          </h3>
          <p class="text-sm text-left text-gray-500">
            {product.description}
          </p>
          <div class="flex justify-between items-center p-1">
            <p class="price__card font-bold text-primary-500 lg:text-lg">
              {product.price}€
            </p>
            <button
              class="bg-primary-500 rounded-xl"
              id="addToCart"
              onclick={() => addToCart(product.id)}
            >
              <p class="text-xm text-amber-50 font-bold m-2 lg:text-lg">
                Ajouter
              </p>
            </button>
          </div>
        </div>
      </Card>
    {/each}
  </section>
</main>
