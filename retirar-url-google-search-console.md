# Documents
script en javascript para google search console, retirar url 1 x 1 automatized 

//Poner acá tu listado de urls.
var array = [
	'url', 
	'url'
]

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

//

async function deleteUrl(){

	//reemplazar por el total de urls. (Perdón por no optimizar esto).
	var cantidad = 736;

	for (i = 0; i < cantidad; i++) {

		console.log('llevamos: ', i)

		//Nueva petición
		document.querySelector('.ZGldwb').click(); 

		await sleep(2000);

		//Agregamos la URL
		document.querySelector('.VfPpkd-fmcmS-wGMbrd').value = array[i];

		await sleep(2000);

		var event = new Event('input', {
		    bubbles: true,
		    cancelable: true,
		});

		document.querySelector('.VfPpkd-fmcmS-wGMbrd').dispatchEvent(event);

		await sleep(2000);

		document.querySelector('.tWntE').click(); 

		await sleep(2000);

		document.querySelector('.sZloWc[data-id="EBS5u"]').click(); 

		await sleep(2000);

	}
   
}

deleteUrl();
