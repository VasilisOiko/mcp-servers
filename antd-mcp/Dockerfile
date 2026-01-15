# Ant Design MCP Server Dockerfile
FROM node:20-slim

# Install git and other dependencies
RUN apt-get update && apt-get install -y \
    git \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Clone the Ant Design MCP server repository
RUN git clone https://github.com/hannesj/mcp-antd-components.git . && \
    npm install

# Extract Ant Design documentation
# Clone the Ant Design repository and extract documentation
RUN git clone https://github.com/ant-design/ant-design.git /tmp/ant-design && \
    node scripts/extract-docs.mjs /tmp/ant-design && \
    rm -rf /tmp/ant-design && \
    test -d /app/data || (echo "Error: Data directory was not created" && exit 1) && \
    test "$(ls -A /app/data)" || (echo "Error: Data directory is empty" && exit 1)

# MCP servers communicate via stdio, no port needed
# But keeping this for potential HTTP-based usage
EXPOSE 3000

# Start the MCP server
CMD ["npm", "start"]
