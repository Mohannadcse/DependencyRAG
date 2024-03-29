<div align="center">
  <img src="docs/DepChatbot.png" alt="Logo" 
        width="400" align="center">
</div>



# DependencyRAG

The chatbot will ask you to provide the name of the `PyPi` package.
It will then the tool `GoogleSearchTool` to get the version of
this package (you can skip this process by providing the intended version).
The chatbot agent will ask to confirm the version number before
proceeding with constructing the dependency graph.

Finally, after constructing the dependency graph, you can ask the chatbot
questions about the dependency graph such as these (specific package names are
used here for illustration purposes, but of course you can use other names):

- what's the depth of the graph?
- what are the direct dependencies?
- any dependency on pytorch? which version?
- Is this package pytorch vunlnerable?
  (Note that in this case the chatbot will consult the 
  tool `GoogleSearchTool` to get an answer from the internet.)
- tell me 3 interesting things about this package or dependency graph
- what's the path between package-1 and package-2? (provide names of package-1
  and -2)
- Tell me the names of all packages in the dependency graph that use pytorch.


This project was originally developed within the project [Langroid](https://github.com/langroid/langroid).
It is a chatbot that answers questions about the dependencies of a given PyPI package.

## Requirements:

This example relies on the `neo4j` Database. The easiest way to get access to neo4j is
by creating a cloud account at [Neo4j Aura](https://neo4j.com/cloud/platform/aura-graph-database/). OR you
can use Neo4j Docker image using this command:

```bash
docker run --rm \
    --name neo4j \
    -p 7474:7474 -p 7687:7687 \
    -e NEO4J_AUTH=neo4j/password \
    neo4j:latest
```

Upon creating the account successfully, neo4j will create a text file contains
account settings, please provide the following information (uri, username,
password, and database), while creating the constructor `Neo4jChatAgentConfig`. 
These settings can be set inside the `.env` file as shown in [`.env-template`](.env-template)


## PyPi Packages Dependency Chatbot

This example uses a `DependencyGraphAgent` 
(derived from [`Neo4jChatAgent`](https://github.com/langroid/langroid/blob/main/langroid/agent/special/neo4j/neo4j_chat_agent.py)).
It auto-generates a `neo4j` knowledge-graph based on the dependency
structure of a given `PyPi` package. You can then ask the chatbot questions
about the dependency graph. This agent uses two tools in addition to those 
already available to `Neo4jChatAgent`:

- DepGraphTool to build the dependency graph for a given pkg version, using the API
   at [DepsDev](https://deps.dev/)
- GoogleSearchTool to find package version and type information. It also can answer
other question from the web about other aspects after obtaining the intended information
from the dependency graph. For examples:
  - Is this package/version vulnerable?
  - does the dpendency use latest version for this package verion?
  - Can I upgrade this package in the dependency graph?

The `Neo4jChatAgent` has access to these tools/function-calls:

- `GraphSchemaTool`: get schema of Neo4j knowledge-graph
- `CypherRetrievalTool`: generate cypher queries to get information from
   Neo4j knowledge-graph (Cypher is the query language for Neo4j)

### Running the example

Run like this:
```
python3 dependencyrag/dependency_chatbot.py
```

Here is a recording shows the example in action:
![Demo](docs/dependency_chatbot.gif)


Run the UI version like this:
```
chainlit run dependencyrag/chainlit/chainlit_dependency_chatbot.py
```

Here is a recording shows the example in action:
![Demo](docs/chainlit_dependency_chatbot.gif)

**NOTE:** the dependency graph is constructed based
on [DepsDev API](https://deps.dev/). Therefore, the Chatbot will not be able to
construct the dependency graph if this API doesn't provide dependency metadata
infromation. 