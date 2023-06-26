# Image base para app em Flask

## Como usar

### Build da imagem

```bash
docker buildx build . --tag <nome_da_imagem>
```

### Executar o container

```bash
docker run -it <nome_da_imagem>
```

# Pacotes instalados

- Poetry

### dsadsa
```yml
    environment:
        name: Environment
        timeout-minutes: 1
        runs-on: ubuntu-latest

        outputs:
          image_name: ${{ steps.variables.outputs.image_name }}
          image_tag: ${{ steps.variables.outputs.image_tag }}

        steps:
          - uses: actions/checkout@v3
            with:
              repository: ${{ github.repository }}
              ref: ${{ github.ref_name }}
              fetch-depth: 0
          
          - name: Setting Variables
            id: variables
            uses: ./.github/actions/utils/variables

          - name: Validate Variables
            env:
              image_name: ${{ steps.variables.outputs.image_name }}
              image_tag: ${{ steps.variables.outputs.image_tag }}

            run: |
              echo "Environment Variables"; \
              echo "${{toJSON(env)}}" | grep -E 'image' ; \
                     
    build:
        name: Build
        timeout-minutes: 10
        runs-on: ubuntu-latest

        steps:
            - uses: actions/checkout@v3
              with:
                repository: ${{ github.repository }}
                ref: ${{ github.ref_name }}
                fetch-depth: 0
            
            - name: Build docker image
              id: build
              uses: ./.github/actions/docker/build
              with:
                image_name: ${{ github.repository }}
                image_tag: ${{ github.ref_name}}

            - name: Upload build artifact
              uses: actions/upload-artifact@v3
              with:
                name: ${{ github.ref_name }}
                path: ${{ github.ref_name }}.tar

    - name: Docker image export
      shell: bash
      run: |
        echo "Running docker export for ${{ github.repository }}:${{ github.ref_name }}" ; \
        docker save \
        --output ${{github.workspace}}/${{ github.ref_name }}.tar \
        ${{ github.repository }}:${{ github.ref_name }}; \
```