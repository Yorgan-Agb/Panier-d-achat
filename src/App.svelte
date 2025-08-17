<script>
  import Icon from "@iconify/svelte";

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
    click = true;
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

<header>
  <h1><span class="lign__title">Fast</span> Shop</h1>
  <nav class="naviguation">
    <ul class="filtered__nav">
      <li><a href="#all">Tous les produits</a></li>
      <li><a href="#all">Alimentaire</a></li>
      <li><a href="#all">Gadget</a></li>
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
      <li class="cart__logo" onclick={isClicked}>
        <Icon icon="mdi:cart" width="24" height="24" />
      </li>
    </ul>
  </nav>
</header>
{#if click === true}
  <section class="user__cart__container">
    <div class="all__cart">
      <h2>Votre <span class="color__cart__title">panier </span></h2>
      {#each userCart as course}
        <article class="user__cart">
          <img src={course.image} alt={course.title} />
          <h3>{course.title}</h3>
          <p class="price__card">{course.price}€</p>

          <div class="button__container">
            <button onclick={() => modifyUserArticleForAdd(course.id)}>+</button
            >
            <p class="quantity__card">{course.quantity}</p>
            <button onclick={() => modifyUserArticleForDecrease(course.id)}
              >-</button
            >
          </div>
          <button
            class="delete__cart"
            onclick={() => deleteUserArticle(course.id)}
            ><Icon icon="line-md:trash" width="24" height="24" /></button
          >
        </article>
      {/each}
      <h4>Total : <span class="total__price">{total.toFixed(2)}</span></h4>
    </div>
  </section>
{/if}
<main>
  <div class="all__products__container">
    <section class="products__container">
      {#each products as product}
        <article class="products__card">
          <div class="container__products">
            <h3 class="title__product">{product.title}</h3>

            <img
              src={product.image}
              alt={product.title}
              class="product__image"
            />
            <p class="description__card">{product.description}</p>
            <p class="price__card">{product.price}€</p>
            <button id="addToCart" onclick={() => addToCart(product.id)}
              >Ajouter au panier</button
            >
          </div>
        </article>
      {/each}
    </section>
  </div>
</main>

<style>
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  h2 {
    font-size: 2em;
  }
  h4 {
    font-size: 1.5em;
  }
  .naviguation {
    table-layout: fixed;
  }
  .lign__title {
    color: #bd2a2e;
  }
  .filtered__nav {
    list-style-type: none;
    display: flex;
    gap: 1em;
    background-color: white;
    border-radius: 5px;
    height: 3em;
    box-shadow:
      rgba(0, 0, 0, 0.25) 0px 54px 55px,
      rgba(0, 0, 0, 0.12) 0px -12px 30px,
      rgba(0, 0, 0, 0.12) 0px 4px 6px,
      rgba(0, 0, 0, 0.17) 0px 12px 13px,
      rgba(0, 0, 0, 0.09) 0px -3px 5px;
    margin: 1em;

    align-items: center;
  }
  a {
    text-decoration: none;
    font-weight: bold;
    color: #bd2a2e;
  }

  .all__products__container {
    width: 60vw;
    background-color: #ddd;
    display: flex;
    align-content: center;
    justify-content: center;
    align-items: center;
  }
  main {
    display: flex;
    justify-content: center;
  }
  .products__container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: repeat(4, 1fr);
  }
  .products__card {
    width: 15em;

    display: flex;
    flex-direction: column;
    justify-content: center;

    background-color: rgb(255, 255, 255);
    margin: 10px;
    border-radius: 10px;

    box-shadow:
      rgba(189, 42, 46, 0.25) 0px 14px 28px,
      rgba(189, 42, 46, 0.22) 0px 10px 10px;
    border: 1px solid #bd2a2e;
  }
  .container__products {
    display: flex;
    flex-direction: column;
  }
  .title__product {
    text-align: center;
    font-size: 1em;
    padding: 20px;
  }

  .description__card {
    text-align: center;
    font-weight: 500;
  }

  .price__card {
    font-weight: bold;
    color: #bd2a2e;
  }

  #addToCart {
    background-color: #bd2a2e;
    border: none;
    height: 3em;
    font-weight: bold;
    cursor: pointer;
    /*  */
  }

  .cart__logo {
    cursor: pointer;
  }
  .user__cart__container {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .all__cart {
    background: #c2c9d6;

    margin-bottom: 10em;

    border-radius: 10px;
    width: 45vw;
  }
  .user__cart {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    align-items: center;

    background-color: rgb(255, 255, 255);
    margin-bottom: 10px;
  }
  .user__cart img {
    height: 8em;
  }
  .user__cart {
    width: 40vw;
    margin-left: 10px;
  }

  .button__container {
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .quantity__card {
    margin: 15px;
  }
  .color__cart__title {
    color: #bd2a2e;
  }
  .total__price {
    color: #bd2a2e;
  }
</style>
