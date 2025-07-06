<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Nutrition Info Finder</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet" />
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      font-family: 'Inter', sans-serif;
      background: #d6e9ff; /* Light blue background */
      margin: 0;
      padding: 30px 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      color: #1f2937;
    }

    h1 {
      margin-bottom: 20px;
      font-weight: 700;
      font-size: 2.2rem;
      color: #0b3d91;
      text-shadow: 0 1px 2px rgba(0,0,0,0.1);
    }

    .search-container {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      justify-content: center;
      margin-bottom: 25px;
      width: 100%;
      max-width: 700px;
    }

    select, input[type="text"] {
      padding: 12px 15px;
      border-radius: 10px;
      border: 1.8px solid #a9c4ff;
      font-size: 16px;
      outline-offset: 2px;
      outline-color: #6b9eff;
      transition: border-color 0.3s ease;
      min-width: 160px;
      flex-grow: 1;
      max-width: 300px;
    }

    select:hover, input[type="text"]:hover,
    select:focus, input[type="text"]:focus {
      border-color: #0b3d91;
    }

    button {
      padding: 12px 25px;
      background-color: #0b3d91;
      color: white;
      border: none;
      border-radius: 10px;
      font-size: 16px;
      cursor: pointer;
      transition: background-color 0.25s ease;
      min-width: 120px;
      flex-shrink: 0;
      box-shadow: 0 3px 7px rgba(11, 61, 145, 0.4);
    }

    button:hover {
      background-color: #07406b;
      box-shadow: 0 5px 12px rgba(7, 64, 107, 0.6);
    }

    #productList {
      margin-top: 30px;
      width: 100%;
      max-width: 700px;
    }

    .product-item {
      display: flex;
      align-items: center;
      background-color: white;
      padding: 18px 20px;
      border-radius: 14px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.12);
      margin-bottom: 18px;
      cursor: pointer;
      transition: transform 0.22s ease, box-shadow 0.22s ease;
      position: relative;
    }

    .product-item:hover {
      transform: translateY(-3px);
      box-shadow: 0 8px 18px rgba(0,0,0,0.16);
    }

    .product-item img {
      width: 70px;
      height: 70px;
      object-fit: cover;
      border-radius: 12px;
      margin-right: 18px;
      flex-shrink: 0;
      background-color: #f0f4ff;
    }

    .product-info {
      display: flex;
      flex-direction: column;
      flex-grow: 1;
    }

    .product-info strong {
      font-size: 18px;
      color: #0b3d91;
      margin-bottom: 4px;
      line-height: 1.2;
    }

    .product-info small {
      color: #64748b;
      font-weight: 500;
    }

    .category-tag {
      position: absolute;
      top: 14px;
      right: 20px;
      background-color: #a9c4ff;
      color: #0b3d91;
      font-size: 12px;
      font-weight: 600;
      padding: 5px 10px;
      border-radius: 12px;
      text-transform: capitalize;
      user-select: none;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    }

    #nutritionInfo {
      margin-top: 40px;
      max-width: 700px;
      width: 100%;
      background-color: white;
      padding: 25px 30px;
      border-radius: 14px;
      box-shadow: 0 2px 7px rgba(0,0,0,0.12);
      color: #1e293b;
    }

    #nutritionInfo h2 {
      margin-top: 0;
      margin-bottom: 20px;
      color: #0b3d91;
      font-weight: 700;
      font-size: 1.8rem;
    }

    #nutritionInfo ul {
      list-style: none;
      padding: 0;
    }

    #nutritionInfo li {
      margin-bottom: 10px;
      font-size: 16px;
      font-weight: 500;
      color: #334155;
    }

    p.no-results {
      text-align: center;
      color: #475569;
      font-weight: 600;
      font-size: 1.1rem;
      margin-top: 30px;
    }

    @media (max-width: 650px) {
      .search-container {
        flex-direction: column;
        align-items: stretch;
      }

      select, input[type="text"], button {
        max-width: 100%;
        flex-grow: 0;
      }

      .product-item {
        flex-direction: column;
        align-items: flex-start;
      }

      .product-item img {
        margin-bottom: 12px;
      }

      .category-tag {
        top: 10px;
        right: 12px;
      }
    }
  </style>
</head>
<body>
  <h1>Nutrition Info Finder</h1>
  <div class="search-container">
    <select id="categorySelect" aria-label="Select product category">
      <option value="">All Categories</option>
      <option value="beverages">Beverages</option>
      <option value="snacks">Snacks</option>
      <option value="dairies">Dairy products</option>
      <option value="cereals">Cereals</option>
      <option value="sweets">Sweets</option>
      <option value="fruits-vegetables">Fruits and Vegetables</option>
      <option value="meats-fish">Meat and Fish</option>
    </select>
    <input type="text" id="searchInput" placeholder="e.g. Nutella" aria-label="Search product" />
    <button onclick="searchProducts()">Search</button>
  </div>

  <div id="productList"></div>

  <div id="nutritionInfo"></div>

  <script>
    // Map dropdown category values to OpenFoodFacts category tags
    // OpenFoodFacts category URLs or slugs may vary; this is a simplified mapping
    const categoryMap = {
      "beverages": "beverages",
      "snacks": "snacks",
      "dairies": "dairies",
      "cereals": "breakfast_cereals",
      "sweets": "sweets",
      "fruits-vegetables": "fruits-vegetables",
      "meats-fish": "meats"
    };

    async function searchProducts() {
      const query = document.getElementById("searchInput").value.trim();
      const selectedCategoryKey = document.getElementById("categorySelect").value;
      const productList = document.getElementById("productList");
      const nutritionInfo = document.getElementById("nutritionInfo");
      productList.innerHTML = "";
      nutritionInfo.innerHTML = "";

      if (!query) return;

      // Build API search URL with query and optional category filter
      let apiURL = `https://world.openfoodfacts.org/cgi/search.pl?search_terms=${encodeURIComponent(query)}&search_simple=1&action=process&json=1&page_size=15`;

      // Append category tag if selected
      if (selectedCategoryKey && categoryMap[selectedCategoryKey]) {
        apiURL += `&tagtype_0=categories&tag_contains_0=contains&tag_0=${categoryMap[selectedCategoryKey]}`;
      }

      try {
        const res = await fetch(apiURL);
        const data = await res.json();

        if (!data.products || data.products.length === 0) {
          productList.innerHTML = "<p class='no-results'>No results found.</p>";
          return;
        }

        data.products.forEach(product => {
          const item = document.createElement("div");
          item.className = "product-item";
          item.onclick = () => showNutrition(product.code);

          // Get first category tag for display (simplified)
          let categoryTag = "";
          if (product.categories_tags && product.categories_tags.length > 0) {
            categoryTag = product.categories_tags[0].replace("en:", "").replace(/-/g, " ");
          }

          item.innerHTML = `
            <img src="${product.image_small_url || 'https://via.placeholder.com/70?text=No+Image'}" alt="${product.product_name || "Product Image"}">
            <div class="product-info">
              <strong>${product.product_name || "Unnamed Product"}</strong>
              <small>Barcode: ${product.code}</small>
            </div>
            ${categoryTag ? `<div class="category-tag">${categoryTag}</div>` : ""}
          `;
          productList.appendChild(item);
        });
      } catch (err) {
        productList.innerHTML = "<p class='no-results'>Error fetching products.</p>";
        console.error(err);
      }
    }

    async function showNutrition(code) {
      const nutritionInfo = document.getElementById("nutritionInfo");
      nutritionInfo.innerHTML = "<p>Loading nutrition data...</p>";

      const apiURL = `https://world.openfoodfacts.net/api/v2/product/${code}?fields=product_name,nutriments`;

      try {
        const res = await fetch(apiURL);
        const data = await res.json();

        if (!data.product || !data.product.nutriments) {
          nutritionInfo.innerHTML = "<p>No nutritional info found.</p>";
          return;
        }

        const n = data.product.nutriments;
        nutritionInfo.innerHTML = `
          <h2>${data.product.product_name || "Product Nutrition Info"}</h2>
          <ul>
            <li><strong>Calories:</strong> ${n["energy-kcal_100g"] ?? "N/A"} kcal per 100g</li>
            <li><strong>Fat:</strong> ${n["fat_100g"] ?? "N/A"} g</li>
            <li><strong>Sugars:</strong> ${n["sugars_100g"] ?? "N/A"} g</li>
            <li><strong>Carbohydrates:</strong> ${n["carbohydrates_100g"] ?? "N/A"} g</li>
          </ul>
        `;
      } catch (err) {
        nutritionInfo.innerHTML = "<p>Error loading nutrition data.</p>";
        console.error(err);
      }
    }
  </script>
</body>
</html>
