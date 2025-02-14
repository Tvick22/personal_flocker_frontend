---
layout: base
menu: nav/home.html
permalink: /allPosts
---
<div id="posts-container" class="py-10 space-y-6"></div>

<link href="https://cdn.jsdelivr.net/npm/daisyui@4.12.19/dist/full.min.css" rel="stylesheet" type="text/css" />

<script type="module">
import { getPostsByType, getImagesByPostId, removePostById } from "{{site.baseurl}}/assets/js/api/posts.js";

const carType = "all";
const postsContainer = document.getElementById("posts-container");

const getPostImages = async (postId) => {
  getImagesByPostId(postId).then((images) => {
    if (images) {
      const formattedImages = [];
      images.forEach((image) => {
        formattedImages.push(`data:image/jpeg;base64,${image}`);
      });
      return formattedImages;
    } else {
      console.error("Failed to fetch images");
    }
  });
}

const removePost = async (postId, postElement) => {
  const removed = await removePostById(postId)
  if (removed) {
    postElement.remove(); // Remove the post element from the DOM
  } else {
    alert("Cannot remove post");
  }
}

getPostsByType(carType).then((posts) => {
  if (posts) {
    const postsContainer = document.getElementById("posts-container");
    const dateNow = new Date();
    const dateNowString = dateNow.getMonth()+1 + "/" + dateNow.getDate() + "/" + dateNow.getFullYear();
    const dateNowHours = dateNow.getHours();
    const orderedPostElements = [...posts]
    const orderedPosts = orderPostByDate(posts)

    orderedPosts.forEach((post, i) => {
      getImagesByPostId(post.id).then((images) => {
        const formattedImages = [];
        images.forEach((image) => {
          formattedImages.push(`data:image/jpeg;base64,${image}`);
        });
        const date = new Date(post.date_posted)
        let dateString = date.getMonth()+1 + "/" + date.getDate() + "/" + date.getFullYear();
        if (dateNowString === dateString) {
          dateString = "Today";
        }
        const postElement = makePostElement(post.title, post.description, dateString, formattedImages, post.id, post.car_type, post.user.name);
        postsContainer.appendChild(postElement)
      });
    });
  } else {
    console.error("Failed to fetch posts");
  }
});

function makePostElement(title, description, date, images, postId, carType, username) {
  const postElement = document.createElement("div");
    postElement.className =
      "w-1/3 max-w-xl mx-auto border border-gray-300 rounded-lg shadow-md bg-white";

    // Add post content
    postElement.innerHTML = `
      <!-- Close Button -->
      <button
        class="closeBtn top-2 left-2 text-gray-600 hover:text-gray-900 rounded-full p-2"
        aria-label="Close">
        &times;
      </button>
      <!-- Header -->
      <div class="flex items-center px-4 py-2">
        <div class="ml-3">
          <h3 class="text-lg font-semibold text-gray-900">${title}</h3>
          <p class="text-sm text-gray-500">${date}</p>
          <p class="text-sm text-gray-500">${carType.toUpperCase()}</p>
          <p class="text-sm text-gray-500">${username.toUpperCase()}</p>
        </div>
      </div>
      <hr class="border-gray-300">

      <!-- Carousel -->
      <div class="relative flex w-full overflow-hidden">
        <div class="carousel relative flex w-full">
          ${images
            .map(
              (image, index) =>
                `
                <img src="${image}" alt="${title}" class="carousel-item w-full">
                `
            )
            .join("")}
        </div>
      </div>

      <!-- Description -->
      <div class="px-4 py-2">
        <p class="text-gray-700">${description}</p>
      </div>
      <hr class="border-gray-300">
    `;

    const closeButton = postElement.querySelector(".closeBtn");
    closeButton.addEventListener("click", () => removePost(postId, postElement));


    return postElement;
}

function orderPostByDate(posts) {
  const sortedPosts = posts

  sortedPosts.sort((post1, post2) => {
    const dateTime1 = new Date(post1["date_posted"])
    const dateTime2 = new Date(post2["date_posted"])

    return dateTime1.getTime()-dateTime2.getTime()
  })
  return sortedPosts
}

</script>
