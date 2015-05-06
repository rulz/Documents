/*
ajax 
$('.product-list').html("<div class='text-center alert alert-info'>Cargando <i class='fa fa-spinner fa-pulse'></i></div>")
      url = url.split('?')[0]
      
      $.ajax({
        method: "GET",
        url: url+'?'+params_order,
        dataType: "html",
      })
      .done(function(html) {
        $('.product-list').html($(html).find('.product-list'))
        window.history.pushState(null, null, url+'?'+params_order);
      })
*/
