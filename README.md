# ludus-monitor: A module from +LUDUS Project.
<h3>Sobre o projeto:</h3>
<p>
  Ludus-Monitor é uma dependência do projeto +LUDUS e é responsável por coletar dados brutos do usuário, como cliques, movimento do mouse e
  tempo percorrido, nos jogos +LUDUS. Estes dados não são usados de forma indevida, e sim, utilizados como base nos projetos acadêmicos, para avaliar a eficiência dos nossos jogos.
  As nossas produções academicas, estão em fase de pesquisa e desenvolvimento e servem de auxílio ao ensino, atendendendo à educação com uma abordagem lúdica e divertida.
  <br>
  <i>
    <strong>Para mais informações</strong>, acesse o <a href = "https://sites.google.com/view/maisludus">site do projeto</a>, que possui uma parceria entre IFSul Câmpus Bagé e Pelotas.
  </i>
</p>

<h1>Início da documentação:</h1>
<p>Sobre este projeto da Unity, utilizaremos dois principais tipos de script. Primeiro, <strong>Monitor</strong>, que é adicionado aos gameObjects que queremos escutar 
  e o <strong>LudusLog</strong>, responsável por gerar logs em formato .JSON.
  Utilizando dos eventos disponíveis ao desenvolvedor com o namespace UnityEngine.EventSystems, conseguimos captar alguns dados brutos através da unity.
  <br>
  
  <strong>OBS:</strong> <i>Algumas interfaces são criadas pelo desenvoldedor.</i>
  <br>

  <h1>Monitor.cs</h1>
  
  ```csharp
using Ludus.SDK.ExportData;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.SceneManagement;
// O monitor não tem muito mistério, você não precisa saber muito sobre a sua implementação, mas o mais importante é anexá-lo a um gameobject.
// É só isso que precisa saber; nós procuramos deixar sua implementação tratar o maior número de erros possível, para não estourar um Debug.LogError automático do sistema.
public class Monitor :
    MonoBehaviour,
    IPointerClickHandler,
    IPointerMoveHandler,
    IMonitorMouseData{...};
  ```

</p>

<h1>Falando sobre o LudusLog (parte mais crucial)</h1>
<p>O <strong>LudusLog</strong> é uma classe .cs, pensada em uma <strong>arquitetura portátil</strong> e não só mais um módulo exclusivo do Framework.
  <strong>Dentro do projeto</strong>, LudusLog é uma classe pública que pode ser instanciada em qualquer script, então sinta-se em casa.
</p>

<h3>Visão geral do script:</h3>
<p>

  ```csharp

using System;
using System.Collections.Generic;
using UnityEngine;
using System.IO;
using Ludus.SDK.Utils;

// Basicamente, utilizando como conceito primordial as tabelas de banco de dados, o nosso LOG Geral será um grande tabela, com vários logs.
// A classe LogCell seria uma Coluna; logController seria a tabela.
// Então, cada célula do log, teria um id, título, descrição e o tempo de execução em que foi executado.
// Depois, esses dados serão adicionados no LogController e assim serão submetidos.
// Por enquanto, eles estão em formato de string, mas pretendemos colocá-los em JSON, e assim exportar para um banco de dados.


namespace Ludus.SDK.ExportData
{
    #region LOG_CELL 
    // Criando a classe LogCell, que seriam as colunas da nossa tabela CreateLog.
    // Elas possuem alguns atributos.
    /*
        1   - Id: é o id da tabela a ser gerado, normalmente são representados por um número inteiro em formato de string.
        2   - Title: Título da célula, pode ser qualquer coisa, mas normalmente para um boa prática, recomendamos colocar um título que seja 
            correspondente ao uso.
        3   - Data: São as informações sobre o log. Você pode covertê-los a um JSON string, e colocar ali. Nesta versão, por enquanto, só há esta opção.
        4   - Date: Pega a description e hora do momento e registra, é feito de foram automática, você nem precisa se preocupar  com isso.
     */
    [System.Serializable]
    public class LogCell
    {
        public string id;
        public string title;
        public string description;
        public string date;
        public LogCell(string title = "", string description = "");
    }
    #endregion LOG_CELL

    #region CREATE_LOG
    /*
        O CreateLog é feito para abrigar todas as colunas em apenas uma tabela; abrigar todos os objetos em um objeto-pai, para termos os dados de forma centralizada.
        Sobre os atributos:
        Lista LogCells - é a lista das células de log.
        Counter - serve para contar a quantidade de colunas, sem precisar usar reports.Count toda hora.
        Title - Título do log pai.
        Data - Feito para abrigar os dados, representado de em string.
     */
    [System.Serializable]
    public class CreateLog
    {
        public string title; // Título do log.
        public string description;
        public List<LogCell> reports = new List<LogCell>(); // Cria uma lista de colunas;
        private int counter = 0; // Contador de colunas.

        public CreateLog(string title = "", string description = "");
        public void addCell(LogCell newLog);
        public void removeCellById(string id);
        public void exportLog();
        public void reset();
        public void redefine(string title = "", string description = "");
    }
    #endregion CREATE_LOG
};
  ```
</p>

<h1>CreateLog, na prática:</h1>
<p>
  
  ```csharp
  
  using UnityEngine;
  using System;
  using System.Generic;


  public class Foo: MonoBehavior
  {
      void Start()
      {
          // Assim como uma tabela, você deve adicionar as colunas.
          // Para isso, primeiro você precisa instanciar o objeto.
          // O construtor de CreateLog aceita dois argumentos principais: new CreateLog(string title, string description);
          // O título é obrigatório, pois é o que identifica o log; a descrição não é necessária, mas recomendamos pois deixa o uso mais organizado.

          CreateLog log = new CreateLog("Meu primeiro Log","Minha descrição");
          // Agora, você precisar criar as colunas, ou seja, os dados que serão colocados nesta tabela, então para isso, você vai usar o método CreateLog.addCell();
          // Você pode criar em um objeto separado ou instanciar diretamente no parâmetro da função.
          
      }
  };

  ```
</p>

