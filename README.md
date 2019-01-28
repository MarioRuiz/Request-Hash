# Request Hash
The purpose of this document is to describe and specify the different keys a Ruby Hash can contain to supply the necessary information for a http request to be executed. This documentation is just a mere indication for Ruby devs and testers so can be used to exchange requests data between libraries, users, gems...

If you want to take these requests to the next level, being able to generate random data following patterns and much more, take a look at our sister project nice_hash: https://github.com/MarioRuiz/nice_hash

The nice_http gem client is adapted to be working with request hashes: https://github.com/MarioRuiz/nice_http

## General Considerations
The keys of the hash should be symbols:

```ruby
  { 
    path: "/example.do",
    headers: {
      "Content-Type": "application/data",
      authentication: "adminexmple"
    }
   }
```

The Request Hashes should be organized on different modules, at least under one module. 
The request hashes should be defined as the response of a method.

```ruby
##################################################
# Swagger Petstore
# version: 1.0.0
# description:
#     A sample API that uses a petstore as an example to demonstrate features in the swagger-2.0 specification
##################################################
module Swagger
  module SwaggerPetstore
    module V1_0_0
      module Pets
        # operationId: findPets, method: get
        # summary: Find the pets
        # description:
        #     Returns all pets from the system that the user has access to
        def self.find_pets()
          {
          }
        end
        .... .....
      end
      
      module Customers
        def self.add_customer()
          {}
        end
        ....
      end
    end
  end
end

```

In case some parameters are required for the request to be send, then they should be added as mandatory parameters on the method, if they are optional then they should be added as optional parameters:

```ruby

        ## id is necessary to be sent
        ## tag and limit are optional parameters
        def self.find_pet_by_id(id, tags: "", limit: "")
          {
            path: "/api/pets/#{id}?tags=#{tags}&limit=#{limit}&"
          }
        end
```


## keys
We describe here all the available keys that are necessary for the different http methods

### path
(String) Supply the path for the request to be executed. By default it is recommended to use relative paths instead of absolute so requests can be executed without any change on different hosts.
This key is used by all http requests.

### headers
(Hash) (Optional) Includes all the necessary headers for your request, including for example the cookies. 

```ruby
  {
    path: '/example?petid=233',
    headers: { 
      'Content-Type': 'application/json',
      'Cookies': {},
      authorization: "Basic Z3Vlc3Q6Z3Vlc3Q=\n"
    }
  }    
```

### method
(Symbol) (Optional) The method that will be used on the request. Possible values: :get, :head, :post, :put, :delete, :patch
Also can include: :connect, :options, :trace

### data
() (Optional) The data for the request that will be used for example to post to the path.
In case data is supplied as a Hash and no Content-Type supplied on headers key, then it will be assumed the data of 'application/json' Content-Type

```ruby
{
  path: '/customer/add',
  data: {
    name: 'Peter Smith',
    address: 'Sol Av, 33',
    city: 'Los Angeles',
    petids: [122, 333, 444]
  }
}
```

### data_examples
(Array) (Optional) Different examples of valid data to be sent. The first example will be taken in consideration as a mock response for libraries using mock responses and no mock_response key supplied on the Request Hash

```ruby
{
  path: '/customer/add',
  data: {
    name: 'Peter Smith',
    address: 'Sol Av, 33',
    city: 'Los Angeles',
    petids: [122, 333, 444]
  },
  data_examples: [
    {
      name: 'Love no address',
      address: '',
      city: 'Los Angeles',
      petids: [1,2]
    },
    {
      name: 'Love no petids',
      address: 'Doom, 33',
      city: 'NYC',
      petids: []
    }    
  ]
}
```

### data_required
(Array) (Optional) IN case of data supplied we can specify here which keys are required to be included on the data request so they are not optional when sending the data

```ruby
{
  path: '/customer/add',
  data: {
    name: 'Peter Smith',
    address: 'Sol Av, 33',
    city: 'Los Angeles',
    petids: [122, 333, 444]
  },
  data_required: [ :name, :petids ]
}

```

### data_pattern
(Hash) (Optional) It will be specified the pattern of the fields if any. It can be supplied a Regular Expression or a String Pattern from the string_pattern gem: https://github.com/MarioRuiz/string_pattern

```ruby
{
  path: '/customer/add',
  data: {
    name: 'Peter Smith',
    address: 'Sol Av, 33',
    city: 'Los Angeles',
    ccid: '463811e7-9760-acf5-9bdb-020073ca3333',
    petids: [122, 333, 444]
  },
  data_pattern: {
    name: :'10-40:Ln', #using string_pattern gem
    ccid: /^[a-fA-F0-9]{8}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{12}$/ # using regular expression
  }
}

```

### mock_response
(Hash) (Optional) Contains the default mock response in case we want to mock the response for testing services that are not implemented yet. It needs to contain at least the keys :code, :message and :data and it can contain :headers hash

```ruby
{
  path: '/example?ownerid=233',
  mock_response: {
    code: 200,
    message: 'OK',
    data: [
      {
        name: 'CatPet',
        color: 'black',
        race: 'unknown',
        cat: true,
        gender: 'male',
        owner: 233
      },
      {
        name: 'Lovely',
        color: 'white',
        race: 'unknown',
        cat: false,
        gender: 'female',
        owner: 233
      },
            
    ]
  }
}

```

### responses
(Hash) (Optional) All the possible responses for the request. They key is the code of the response and the value is the response data body.

```ruby
        def self.find_pets()
          {
            path: "/api/pets",
            responses: {
              '200': {
                message: "pet response",
                data: [
                  {
                    name: "Doop",
                    tag: "",
                    id: 0,
                  },
                ],
              },
              '500': {
                message: "unexpected error",
              },
            },
          }
        end

```








