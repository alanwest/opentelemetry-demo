# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0
# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# https://mcr.microsoft.com/v2/dotnet/sdk/tags/list
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0.100-1-alpine3.18 AS builder
ARG TARGETARCH

# Building gRPC applications on Alpine Linux requires additional steps for protoc to work
# https://learn.microsoft.com/aspnet/core/grpc/troubleshoot#building-grpc-on-alpine-linux
RUN apk add grpc-plugins
ARG PROTOBUF_PROTOC=/usr/bin/protoc
ARG GRPC_PROTOC_PLUGIN=/usr/bin/grpc_csharp_plugin

WORKDIR /usr/src/app/

COPY ./src/cartservice/ ./
COPY ./pb/ ./src/protos/

RUN dotnet restore ./src/cartservice.csproj -v d -a $TARGETARCH

RUN dotnet publish ./src/cartservice.csproj -v d -a $TARGETARCH --no-restore -o /cartservice

# -----------------------------------------------------------------------------

# https://mcr.microsoft.com/v2/dotnet/runtime-deps/tags/list
FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.0-alpine3.18

WORKDIR /usr/src/app/
COPY --from=builder /cartservice/ ./

EXPOSE ${CART_SERVICE_PORT}
ENTRYPOINT [ "./cartservice" ]
