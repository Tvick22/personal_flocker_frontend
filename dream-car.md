---
layout: base
title: Dream Car 
search_exclude: true
menu: nav/home.html
---

<section id="featured-cars" class="py-20 bg-gray-100 h-screen grid grid-cols-2 justify-center">
    <div class="grid grid-cols-1 md:grid-cols-2 gap-8 container mx-auto">
        <!-- Car Talk Card -->
        <a href="{{site.baseurl}}/car-talk" class=" h-full bg-white rounded-lg shadow-lg overflow-hidden transform transition-transform duration-500 hover:scale-105">
            <img src="https://exclusivecarregistry.com/render-images?imgid=262153" alt="Car Talk" class="w-full h-64 object-cover">
            <div class="p-6">
                <h3 class="text-3xl font-bold mb-2">Car Talk</h3>
                <p class="text-xl text-gray-700">Chat with other users about your dream cars!</p>
            </div>
        </a>
        <!-- Mechanical Help Card -->
        <a href="{{site.baseurl}}/mechanical-help" class="h-full bg-white rounded-lg shadow-lg overflow-hidden transform transition-transform duration-500 hover:scale-105">
            <img src="https://img.freepik.com/free-vector/pliers-hammer-screwdriver-cartoon-icon-illustration-tools-object-icon-concept-isolated-flat-cartoon-style_138676-2155.jpg" alt="Mechanical Help" class="w-full h-64 object-cover">
            <div class="p-6">
                <h3 class="text-3xl font-bold mb-2">Mechanical Help</h3>
                <p class="text-xl text-gray-700">Get help from other users sharing your car-related issues!</p>
            </div>
        </a>
    </div>
</section>
