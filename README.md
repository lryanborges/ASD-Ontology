<div align="center">
  <img src="https://github.com/user-attachments/assets/cac4b459-d438-46ad-8fde-8040b5de2496" width="64px" />
</div>

<h2>TEA Ontology</h2>
O foco dessa ontologia é a caracterização de pessoas adultas com TEA dentro do contexto genético e sensorial, com menções à diferenciação entre determinados padrões de comportamento masculino e feminino dentro do transtorno.

<h3>Link de Acesso ao Colab com implementação do OWLReady2: </h3>
<a href="https://colab.research.google.com/drive/1eQuP5CJS34VLTMMalKYUT8yPXPqyYIsD?usp=sharing">https://colab.research.google.com/drive/1eQuP5CJS34VLTMMalKYUT8yPXPqyYIsD?usp=sharing</a>

<h3>Visões do OntoUML</h3>

1ª - EspectroGeralTEA: voltado à relação entre o Adulto com TEA e a existência de dois tipos de características, além de conter a diferenciação entre diferentes tendências de comportamento baseado no gênero;

2ª - CaracterísticasSensoriais: voltado à especificação das características sensorais do Adulto com TEA, onde são divididas entre diferentes tipos baseado no sentido humano que mais influencia em cada uma delas. Sendo os sentidos: Tato, Paladar, Audição, Olfato e Visão;

3ª - CaracterísticasGenéticas: voltado à especificação das características genéticas do Adulto com TEA, havendo relações do TEA com outros transtornos do neurodesenvolvimento e com as condições parentais.

<h3>OWL</h3>
Até o momento, a importação do modelo do OntoUML ao Protégé não causa nenhum problema, porém ainda não foram feitas espeficicações para com as object proprieties e suas aplicações nas classes.

<h3>Considerações</h3>

- No OWL, existem algumas relações "inferidas" do modelo do OntoUML com o modificador "inverse" que não são possíveis remover pois está obrigatoriamente em algum dos lados da relação. Ao se remover de um lado, aparece do outro e vice-versa.

- Estou considerando que esta ontologia irá auxiliar no diagnóstico de adultos com TEA, onde a integração social pode ter sido melhor desenvolvida ao decorrer de sua vida e suas características mais marcantes deixadas de lado.
  
  - Por levar em conta essa integração social que achei importante existir uma diferenciação de gênero, pois sabe-se que estes são fatores que estão bastante relacionados.
    
  - Além disso, é provável que minha ontologia caiba mais às pessoas com "baixos níveis de TEA", as quais são mais dificilmente diagnosticadas quando crianças.

<h3>Possíveis consultas SPARQL a serem realizadas na versão em inglês da ontologia:</h3>

<h4>Consulta para buscar o paciente, nome e idade</h4>
```bash
    PREFIX asd: <http://asdontology.com#>

    SELECT ?subject (STR(?rdfName) AS ?nome) (STR(?rdfAge) AS ?idade)
    WHERE {
        ?subject asd:hasName ?rdfName ;
                 asd:hasAge ?rdfAge
    }
```

<h4>Consulta para buscar nome, característica sensorial e característica genética das instâncias</h4>
```bash
    PREFIX asd: <http://asdontology.com#>

    SELECT (STR(?rdfName) AS ?nome) ?caracteristicaSensorial ?caracteristicaGenetica
    WHERE {
        ?subject asd:hasName ?rdfName ;
                 asd:hasSensoryTrait ?caracteristicaSensorial.
        OPTIONAL { ?subject asd:hasGeneticTrait ?caracteristicaGenetica. }
    }
```

<h4>Consulta para buscar nome e tendência feminina das instâncias</h4>
```bash
    PREFIX asd: <http://asdontology.com#>

    SELECT ?rdfName ?tendenciaFeminina
    WHERE {
        ?patient a asd:Patient ;
                 asd:hasName ?rdfName .

        OPTIONAL {
            ?patient asd:patientHasFemaleTendency ?tendenciaFeminina .
        }

        FILTER (BOUND(?tendenciaFeminina))
    }
```

<h4>Consulta para buscar nome, características genéticas e tendência de gênero das instâncias</h4>
```bash
    PREFIX asd: <http://asdontology.com#>

    SELECT (STR(?rdfName) AS ?name) ?geneticTrait ?genderTendency
    WHERE {
        ?subject asd:hasName ?rdfName ;
                 asd:hasGeneticTrait ?geneticTrait .
        OPTIONAL {
            ?subject asd:patientHasMaleTendency ?genderTendency .
        }
        OPTIONAL {
            ?subject asd:patientHasFemaleTendency ?genderTendency .
        }
    }
```

<h4>Consulta para buscar nome e características sensoriais (subclasses de Taste)</h4>
```bash
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX asd: <http://asdontology.com#>

    SELECT (STR(?rdfName) AS ?name) ?sensoryTrait
    WHERE {
        ?subject asd:hasName ?rdfName ;
                 asd:hasSensoryTrait ?sensoryTrait .
        ?sensoryTrait rdfs:subClassOf asd:Taste .
    }
```

<h4>Consulta para buscar nome, número de características sensoriais, características genéticas e tendências de gênero</h4>
```bash
    PREFIX asd: <http://asdontology.com#>

    SELECT
        (STR(?rdfName) AS ?nome)
        (STR(COUNT(DISTINCT ?sensoryTrait)) AS ?numCaracteristicasSensorial)
        (STR(COUNT(DISTINCT ?geneticTrait)) AS ?numCaracteristicasGeneticas)
        (STR((COUNT(DISTINCT ?malePatient) + COUNT(DISTINCT ?femalePatient))) AS ?numTendenciasDeGenero)
    WHERE {
        ?subject asd:hasName ?rdfName ;
                 asd:hasSensoryTrait ?sensoryTrait .

        OPTIONAL { ?subject asd:hasGeneticTrait ?geneticTrait . }
        OPTIONAL { ?subject asd:patientHasMaleTendency ?malePatient . }
        OPTIONAL { ?subject asd:patientHasFemaleTendency ?femalePatient . }
    }
    GROUP BY ?rdfName
```