- Definindo um Esquema :

    - Chamando a API Query :

    # A API de consulta é implementada no estilo REST. Isso significa que você pode chamar a API de consulta de uma variedade de ambientes de desenvolvimento e até mesmo de seu navegador. A sintaxe correta é a seguinte:

    Substitua <APIKEY> e <SQL_ENCODED_STRING> pelos valores corretos.

    [
        curl -H 'apikey: {generated_api_key}'   EUGENIO_URL/api/v1/data/query?sql=<SQL_ENCODED_STRING>
    ]
    *Eugenio apoia o dialeto Impala SQL.*

    # A consulta sql deve ser codificada por url.
    # Eugenio apoia o dialeto Impala SQL. Para obter mais detalhes, consulte o Impala SQL Language Reference

    # Um exemplo de consulta válida:
    # Substitua o e com seus nomes de organização e esquema.

    [
        SELECT * FROM <organization>_sandbox.v_<schema>   ORDER BY created_at DESC LIMIT 10
    ]