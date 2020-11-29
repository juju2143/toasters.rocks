---
title: Mathieu Bock-Côté Simulator 2020
slug: mathieu-bot-cote
publishDate: 2019-12-24T04:05:50.000Z
lastmod: 2019-12-24T05:32:20.000Z
tags: [Experiments]
---

Mathieu Bock-Côté est l'un de ces chroniqueurs reconnu pour ses pavés qu'on retrouve presque quotidiennement dans le *Journal de Montréal*, donc je me suis dit qu'il serait un sujet parfait pour entraîner une intelligence artificielle sur ses textes. Donc si vous avez toujours rêvé de lui poser une question, ou lui suggérer un sujet, vous pouvez le faire ci-dessous avec un Bock-Côté virtuel! Ou sinon vous pouvez juste cliquer sur le bouton et il va quand même émettre un bout de chronique.

<p><textarea id="bot-query" style="width: 100%" placeholder="Posez une question ou mettez un sujet (optionnel)" maxlength="280"></textarea></p>

<p style="text-align: center"><button id="bot-generate">Générer une chronique</button></p>

<div id="bot-generated-text"><em>Le générateur n'est pas disponible pour l'instant. Il est probablement parti acheter des cigarettes au dépanneur.</em></div>

---

Disclaimer: Ce générateur ne reflète pas nécessairement l'opinion émise par le vrai Mathieu Bock-Côté ou le *Journal de Montréal.* Je compte sur vous pour ne pas en abuser (genre, copiez pas le texte généré en disant que c'est le vrai, ça se fait pas, ou n'envoyez-moi pas un DDoS, ça coûte cher de Google Cloud), et si vous êtes le susmentionné Bock-Côté ou le *Journal* et vous n'aimez pas ça, on peut toujours s'en parler :)

Et si vous vous demandez comment ça marche, il y a [ce billet ici](https://minimaxir.com/2019/09/howto-gpt2/) qui explique comment ça marche. J'ai suivi le tutorial avec un corpus de texte que j'ai pompé sur le site du Journal de Montréal, je l'ai entraîné durant 6 heures sur le petit modèle 124M, j'ai uploadé ça sur Google Cloud, écrit un petit script pour interagir avec le tout et voilà! Le modèle GPT-2 original utilisé ci-dessus a été conçu et entraîné en anglais, mais je trouve qu'il s'en sort pas trop mal en français. Je risque d'en faire un autre avec Richard Martineau, si vous avez aimé. Sur ce, [suivez mon blogue](/signin/) ou mon [Twitter](https://twitter.com/juju2143) pour d'autres expérimentations weird du genre, ou envoyez-moi un peu de cash sur [Patreon](https://patreon.com/juju2143) pour pouvoir me soutenir financièrement et financer ces expérimentations qui n'ont somme toute plus d'allure :)

Sur ce, joyeuses fêtes!

<script>
$("#bot-generate").click(function(){
    $("#bot-generated-text").html("<em>Je pense... (ça peut prendre une minute ou deux)</em>");
	$.ajax({
		url: "https://mathieubotcote-j4sh32wzea-ue.a.run.app",
		data: {
            length: 500,
            prefix: "\n"+$("#bot-query").val()
        }
    })
    .done(function(data){
        $("#bot-generated-text").html("");
        var text = data.text.split("\n");
        console.log(text);
        for(var i = 0; i < text.length; i++)
        {
        	var $p = $("<p>").html(text[i]);
      		$("#bot-generated-text").append($p);
        }
    })
    .fail(function(){
        $("#bot-generated-text").html("<em>Le générateur n'est pas disponible pour l'instant. Il est probablement parti prendre un café.</em>");
    });
});
</script>