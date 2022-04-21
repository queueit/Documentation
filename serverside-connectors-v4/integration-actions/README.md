# Understanding integration actions
The diagram below illustrates how Actions from GO Queue-it Platform are used by a Connector SDK. An action is matched when one of its connected trigger's conditions are meet. 

For simplicity Queue-it infrastructure is not part of the diagram, so it only illustrates what is happening at the infrastructure running the Connector. All 302 Redirects are to Queue-it's infrastructure which will do 302 Redirect back to infrastructure running the Connector.

![Integration actions explained](https://github.com/queueit/Documentation/blob/main/serverside-connectors/integration-actions/actions-explained.png)