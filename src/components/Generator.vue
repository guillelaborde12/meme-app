<template>
  <div class="generator">
    <div class="generator__form">
      <div class="generator__text">
        <input
          name="superior"
          v-model="meme.superior"
          placeholder="Texto superior"
        />
        <input
          name="inferior"
          v-model="meme.inferior"
          placeholder="Texto inferior"
        />
      </div>
      <div class="generator__image">
        <!-- Componente Selector de Imagen -->
        <input
          type="file"
          @change="handleImageChange"
          accept="image/*"
          style="margin-bottom: 20px"
        />
        <div v-if="meme.selectedImage" class="selected-image">
          <img :src="meme.selectedImage" :alt="meme.superior" />
          <div class="image-overlay">
            <div class="text-overlay top">
              <p>{{ meme.superior }}</p>
            </div>
            <div class="text-overlay bottom">
              <p>{{ meme.inferior }}</p>
            </div>
          </div>
        </div>
        <a
          v-if="meme.selectedImage"
          class="download-button"
          @click="downloadImage"
        >
          Descargar Meme
        </a>
      </div>
    </div>
    <div class="generator__preview">
      <div class="generator__preview_content">
        <img
          v-if="meme.selectedImage"
          :src="meme.selectedImage"
          alt="Preview Image"
        />
      </div>
      <div
        v-if="meme.superior || meme.inferior"
        class="generator__preview_text"
      >
        <p class="superior-text">{{ meme.superior }}</p>
        <p class="inferior-text">{{ meme.inferior }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import html2canvas from "html2canvas";

const meme = ref({
  superior: "yo haciendo un generador de memes online",
  inferior: "para los pibes de mi secundaria",
  selectedImage:
    "https://pbs.twimg.com/media/EQYY4JpWkAE6RpF?format=jpg&name=900x900",
});

// Función para manejar el cambio de imagen
const handleImageChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = () => {
      meme.value.selectedImage = reader.result;
    };
    reader.readAsDataURL(file);
  }
};

// Función para descargar el contenido visible como una imagen
const downloadImage = () => {
  const selectedImage = document.querySelector(".selected-image");

  if (selectedImage) {
    // Usar html2canvas para capturar el div selectedImage como un lienzo
    html2canvas(selectedImage).then((canvas) => {
      // Convertir el lienzo a datos de imagen en base64
      const imageData = canvas.toDataURL("image/png");

      // Crear un elemento de enlace temporal
      const link = document.createElement("a");
      link.href = imageData;
      link.download = "meme_generated.png";

      // Adjuntar el enlace al cuerpo y activar la descarga
      document.body.appendChild(link);
      link.click();

      // Limpiar
      document.body.removeChild(link);
    });
  }
};
</script>

<style lang="scss" scoped>
// variables
$desktop-breakpoint: 768px;

.generator {
  display: flex;
  flex-direction: row;
  width: 100%;
  max-width: 800px;

  &__form {
    flex: 1;
    margin-right: 20px;
  }

  &__text {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  &__image {
    position: relative; // Asegurar el contexto de posición para la superposición
    margin-top: 36px !important;

    .selected-image {
      position: relative; // Asegurar el contexto de posición para la superposición

      img {
        max-width: 100%;
        height: auto;
        display: block;
      }

      .image-overlay {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding: 10px;
        box-sizing: border-box;

        .text-overlay {
          color: white;
          font-size: 40px;
          font-weight: 800;
          padding: 10px;
          text-align: center;

          p {
            margin: 0;
            font-size: 20px;
            -webkit-text-stroke: 1px black;
            font-family: impact;
          }
        }

        .top,
        .bottom {
          width: 95%;
        }
      }
    }
  }

  &__preview {
    margin-top: 50px;
    display: none; // Ocultar contenido de vista previa predeterminado a la derecha en modo escritorio
    flex: 1;
    margin-left: 20px;

    &_content {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100%;

      img {
        max-width: 100%;
        height: auto;
        margin-bottom: 10px;
      }
    }

    &_text {
      text-align: center;

      .superior-text {
        font-size: 18px;
        font-weight: bold;
        margin-bottom: 5px;
      }

      .inferior-text {
        font-size: 18px;
        margin-top: 5px;
      }
    }
  }

  .download-button {
    display: block;
    width: 93.5%;
    padding: 10px;
    margin-top: 10px;
    text-align: center;
    background-color: #007bff;
    color: white;
    text-decoration: none;
    border-radius: 5px;
    transition: background-color 0.3s ease;

    &:hover {
      background-color: #0056b3;
    }
  }
}
</style>
