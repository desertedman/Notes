# Basic Rendering

<!--toc:start-->

- [Basic Rendering](#basic-rendering)
  - [Creating a Window](#creating-a-window)
  - [Rendering a Triangle](#rendering-a-triangle)
    - [Storing vertices](#storing-vertices)
    - [Shaders](#shaders)
      - [Compiling Shaders](#compiling-shaders)
    - [Vertex Attributes](#vertex-attributes)
  - [Further Information](#further-information)
  <!--toc:end-->

## Creating a Window

- Use GLAD and GLFW libraries to easily create a window
  - glad must be included before glfw, like so:

    ```cpp
    #include <glad/glad.h>
    #include <GLFW/glfw3.h>
    ```

  - All code is fairly boiler plate here and can be found on [LearnOpenGL](https://learnopengl.com/Getting-started/Creating-a-window)

## Rendering a Triangle

### Storing vertices

- Vertex Buffer Objects (VBOs) store vertices in GPU memory
  - Typical structure is as follows:

    ```cpp
    unsigned int VBO;
    glGenBuffers(1, &VBO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW)
    ```

    - Must first generate a unique buffer ID with `glGenBuffers()`
      - Type of VBO is `GL_ARRAY_BUFFER`
    - Buffers can be bound by `glBindBuffer()`
    - Any further calls to `GL_ARRAY_BUFFER` target will be used to configure
      bound buffer by `glBindBuffer()`
    - Copy vertex data into buffer with `glBufferData()`

### Shaders

- Vertex shader
  - Takes input as a single vertex, applies transformations to 3D coordinates
  - Example vertex shader in GLSL:

    ```glsl
    #version 330 core
    layout (location = 0) in vec3 aPos;

    void main()
    {
      gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
    }
    ```

    - Declare input and output variables with `in` and `out` keywords respectively
    - `gl_Position` seems to be a built in language keyword of type `vec4`
    - Note the `(location = 0)`; this is the location of the vertex attribute, and
      corresponds to the index given in `glVertexAttribPointer()` and `glEnableVertexAttribArray()`

- Fragment shader
  - Calculates final color output of a pixel; also known as pixel shader
  - Example fragment shader:

    ```glsl
    #version 330 core
    out vec4 FragColor;

    void main()
    {
      FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
    }
    ```

    - Only requires one output variable that defines final color output

#### Compiling Shaders

- Shaders must be compiled at runtime from GLSL source code
- Example:

  ```cpp
  unsigned int vertexShader;
  vertexShader = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
  glCompileShader(vertexShader);
  ```

  - Generate unique ID for shader, create vertex shader object, attach shader
    source code to object, and finally compile object

- Must generate final shader program with multiple shaders linked together
  - Links output of one shader into the input of another
  - Example:

    ```cpp
    unsigned int shaderProgram;
    shaderProgram = glCreateProgram();
    glAttachShader(shaderProgram, vertexShader);
    glAttachShader(shaderProgram, fragmentShader);
    glLinkProgram(shaderProgram);
    glUseProgram(shaderProgram);
    ```

    - `glUseProgram()` will use the input shader program for every shader and
      rendering call after

### Vertex Attributes

- Must tell OpenGL how to interpret vertex data before rendering
- Example:

  ```cpp
  float vertices[] = {
  -0.5f, -0.5f, 0.0f,
    0.5f, -0.5f, 0.0f,
    0.0f,  0.5f, 0.0f
  };

  glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void *)0);
  glEnableVertexAttribArray(0);
  ```

<img src="https://learnopengl.com/img/getting-started/vertex_attribute_pointer.png" alt="Vertex attribte pointer setup of OpenGL VBO"/>

- Position data is stored as 4 byte floats
- Each vertex has 3 values
- No space between each set of 3 values; i.e. tightly packed array
- First value of data is in beginning of buffer; i.e. index/offset of 0

```cpp
glVertexAttribPointer(GLuint index, GLint size, GLenum type, GLboolean
                      normalized, GLsizei stride, const GLvoid * pointer)
```

- `index` specifies vertex attribute to configure. Corresponds to location of
  attribute in shader (ex. `layout (location = 0)`)
- `size` refers to size of vertex attribute. Position is a `vec3` so it is
  composed of 3 values
- `type` is type of data; in this case they are `GL_FLOAT`s
- `stride` is distance between consecutive vertex attributes. Since each vertex
  attribute is 3 floats, the next set of position data is `3 * sizeof(float)`
  away from the first
- `pointer` is offset/index of where the vertex attribute starts
  - Type of `pointer` is `void *`, so it must be casted via `(void *)0`

- Finally, vertex attribute must be enabled via `glEnableVertexAttribArray
(GLuint index)`, where `index` is location of attribute

## Further Information

- [LearnOpenGL - Hello Triangle](https://learnopengl.com/Getting-started/Hello-Triangle)
