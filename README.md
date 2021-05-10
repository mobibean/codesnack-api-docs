**The API is deprecated. Please, contact me directly by E-mail bushevuv@gmail.com if you are interesting in API.**

This document describes Cloud Compiler API.

### Create new Task

`POST https://_deprecated_/tasks`

#### Request Headers

- **Authorization** header is required, contains a client API key.
- **Content-Type** header is required, should equal to "multipart/form-data".

#### Request Body

The body should be a form-data (detect multipart).

- **`language`** - A language name. See the table below for supported languages. Required.
- **`version`** - A language version tag. See the table below for supported languages version tags. Required.
- **`platform`** - A platform name. See the table below for supported platforms. Required.
- **`project`** - Project files within a ZIP archive. Required.
- **`pre-exec-command`** - The command that will be executed after unzipping the project and before the execution. You can compile code here or prepare the environment. Optional.
- **`exec-command`** - The command to run the program. It can contain input arguments. Required.

#### Response

The JSON object will be returned on success:

```json
{
  "id": "29e986f3-b2d0-4929-8e5c-e62311f89e60"
}
```

#### Possible errors

- **`401`** - API Key missed or invalid.
- **`400`** - One of the following:
    - `TOO_MANY_CONCURRENT_TASKS` - Limit of simultaneously running tasks reached.
    - `TOO_MANY_TASKS` - Limit of tasks per month reached.

, where `id` - task id.

### Run a Task

Once Task `id` obtained from the API, you can connect to Websocket real-time API and bind input and output channels to 
a user interface (like a console). Right after Websocket will be connected and the connection will be initiated
`pre-exec-command` (is any) and `exec-command` commands will be executed automatically on the API side.

Websocket endpoint to connect: `https://api-v2.cloud-compiler.com`

The Websocket connection itself, should be established from a client side (no API keys will be needed at this point).

#### Client -> API events

- **`initiate`** - Client must initiate a connection right after connect is established. `taskId` should be provided in this event.
- **`kill`** - Once API receives `kill` event `pre-exec-command` or `exec-command` (depends on what's currently running) will be killed and the task will be terminated.
- **`stdin`** - Once API receives `stdin` it forward it to a process with running `exec-command-finished` command.

#### API -> Client events

- **`pre-exec-command-started`** - The event fired once `pre-exec-command` command has started on API side. The command itself will be available in a `data` event property.
- **`pre-exec-command-finished`** - The event fired once `pre-exec-command` commend has finished on API side. The event will contain `exitCode` and `killed` properties.
- **`exec-command-started`** - The event fired once `exec-command` command has started on API side. The command itself will be available in a `data` event property.
- **`exec-command-finished`** - The event fired once `exec-command` commend has finished on API side. The event will contain `exitCode` and `killed` properties.
- **`stdout`** - The event fired on `stdout` event from `pre-exec-command` or `exec-command`. The output string will be available in a `data` event property.
- **`stderr`** - The event fired on `stderr` event from `pre-exec-command` or `exec-command`. The output string will be available in a `data` event property.

## Available Languages and Version Tags

| Language | Version Tag |
| ------------- | ------------- |
| C | gcc-9.2.0, gcc-8.3.0, gcc-7.4.0 |
| C++ | gcc-9.2.0, gcc-8.3.0, gcc-7.4.0 |
| JavaScript (Node.js) | 12.10.0, 10.16.3, 8.16.1 |
| Java | openjdk-14, openjdk-13, openjdk-12 |
| PHP | 7.4, 7.3, 7.2 |
| Python | 3.8, 3.7, 3.6, 2.7.13 |
| Ruby | 2.7, 2.6, 2.5 |
| Swift | 5.0, 4.2, 4.1 |
| Go | 1.13, 1.12 |
| Kotlin | 1.3, 1.2, 1.1 |

## Available Platforms

| Platform |
| ------------- |
| Linux |
| Windows |
| MacOS |
