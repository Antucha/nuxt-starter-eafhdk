<template>
  <div>
    <div id="leomessi">
      <h1 id="welcome2">Hola, mundo cruel</h1>
      <div class="bg-secondary text-light">
        ✅ Para todos los padres, madres y cuidadores: Hoy es el día perfecto
        para divertirse con los niños y pasar tiempo juntos. ¡Celebremos unidos
        la maravillosa aventura de la niñez! #DíaDelNiño #CuidaSuSalud 👦🏻👧🏽
        <samp>&lt;b-card-body&gt;</samp> block of the
        <samp>&lt;b-card&gt;</samp> component. Notice the padding between the
        card's border and this gray <samp>&lt;div&gt;</samp>.
        <br />
        <img
          height="500px"
          src="https://firebasestorage.googleapis.com/v0/b/hackernoon-app.appspot.com/o/images%2FMYtW3HmjyRZIy54jKC8y5dnTZ6f1-vrt3uhu.jpeg?alt=media&token=6e30c2ab-fc98-4e94-93d9-1e7c7b008365&auto=format&fit=max&w=1920"
        />
      </div>
    </div>
    <hr />
    <div id="mountain">
      <div class="bg-secondary text-light">
        SPAIN ARE WORLD CHAMPIONS!!! 🇪🇸 #BeyondGreatness | #FIFAWWC
        <samp>&lt;b-card-body&gt;</samp> block of the
        <samp>&lt;b-card&gt;</samp> component. Notice the padding between the
        card's border and this gray <samp>&lt;div&gt;</samp>.
        <br />
        <img width="500px" src="./junta.png" />

        <h1 id="welcome2">Hola, mundo cruel</h1>
      </div>
    </div>
    <button @click="generatePDF">Producir pdf</button>
  </div>
</template>
<script>
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';

export default {
  methods: {
    convertBase64ToImageAndDownload(base64Data, fileName) {
      // Convertir Base64 a un objeto Blob
      const byteCharacters = atob(base64Data.split(',')[1]);
      const byteArrays = [];
      for (let offset = 0; offset < byteCharacters.length; offset += 512) {
        const slice = byteCharacters.slice(offset, offset + 512);
        const byteNumbers = new Array(slice.length);
        for (let i = 0; i < slice.length; i++) {
          byteNumbers[i] = slice.charCodeAt(i);
        }
        const byteArray = new Uint8Array(byteNumbers);
        byteArrays.push(byteArray);
      }
      const blob = new Blob(byteArrays, { type: 'image/png' });

      // Crear un enlace de descarga y asignar la imagen Blob
      const downloadLink = document.getElementById('downloadLink');
      downloadLink.href = URL.createObjectURL(blob);
      downloadLink.download = fileName;

      // Simular un clic en el enlace para iniciar la descarga
      downloadLink.click();

      // Liberar recursos
      URL.revokeObjectURL(downloadLink.href);
    },
    async generatePDF() {
      const pdf = new jsPDF('p', 'mm', 'a4');
      const pageWidth = pdf.internal.pageSize.getWidth();
      const pageHeight = pdf.internal.pageSize.getHeight();

      const elementsToCapture = ['#leomessi', '#mountain'];

      for (const elementId of elementsToCapture) {
        const canvas = await html2canvas(document.querySelector(elementId), {
          imageTimeout: 100000,
          useCors: true,
          allowTaint: true,
        });
        const imgData = canvas.toDataURL('image/png');
        const imgProps = pdf.getImageProperties(imgData);
        const imgWidth = imgProps.width;
        const imgHeight = imgProps.height;

        if (imgWidth <= pageWidth && imgHeight <= pageHeight) {
          pdf.addImage(imgData, 'PNG', 0, 0, imgWidth, imgHeight);
        } else {
          const scaleFactor = Math.min(
            pageWidth / imgWidth,
            pageHeight / imgHeight
          );
          const scaledWidth = imgWidth * scaleFactor;
          const scaledHeight = imgHeight * scaleFactor;

          pdf.addImage(
            imgData,
            'PNG',
            (pageWidth - scaledWidth) / 2,
            (pageHeight - scaledHeight) / 2,
            scaledWidth,
            scaledHeight
          );
        }

        // Agregar una nueva página si no es el último elemento
        if (elementId !== elementsToCapture[elementsToCapture.length - 1]) {
          pdf.addPage();
        }
      }

      pdf.save('documento.pdf');
    },
  },
};
</script>
