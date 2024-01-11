npx create-nx-workspace --preset=angular
?workspace name (eg:org name) shirt-shop
?application name             shirt-shop
?Default stylesheet format    select  css
choose eslint
use Nx cloud :yes

2)cd shirt-shop

apps -> shirt-shop(angular application folder created)
     -> shirt-shop-e2e
libs (most of the code for our application will here)	

run angular application:

3)npm start:
application run with ng serve command it self


to work with nest :

5)npm i -D @nrwl/nest (this gives us the plugin)
6)nx generate @nrwl/nest:application api(we can give any name other than api also)
installed nest required pacakages 

with this updates dones in package.json , .vscode/extensions.json
angular.json
nx.json
jest.config.js

apps --> api --> src --> app --> app.controller.ts
app.service.tsapp.modules 


7) to run nest application 
nx serve api


PRISMA 
-----------
to work with prisma for db

for regular dependency
8)npm i @prisma/client // its type safe to work with databases

9)npm i -D prisma //its deve dependency, will give prisma cli

10)npx prisma init // to initilize prisma

we will get new directory called prisma after prisma initilization

inside prisma folder we will get schema.prisma file
which have db connection and need to add db tables and
relation between tables

schema.prisma consists

datasource db{
provider = "sqlite" // by default comes with postgress
url = "file:./dev.db"
}

generator client {
provider = "prisma-client-js"
}

// need to create the model to establish the table
model Product{
id string @id @default(cuid()) // collision resistant unique id
name String
description String
image String 
price Int
sku String
}

// we need to know , get this into database

we need to create db first place
then we need to get the table into it

12)two ways we can get data push into db
this is useful  local db level scenario

a) npx prisma db push --preview-feature

b)use production level prisma migrate
npx prisma migrate dev

// migrate , we would say migrate dev using prisma migrate will
creae a migrations directory ,it will track all of the sql that is
used to construct our db tables and make changes to them over the
course of time and the benfit of having stroed migration is that we
then run them in our production settings and affect our db in production
however we work in local development here the db push command is fine

13) npx prisma studio // to take a look inside our database

when we do that we will get the product table with all of our columns

// to push data into table we can use database seeding

create seed.ts file into prisma folder
prisma/seed.ts

import { PrismaClient } from '@prisma/client'⁪
const prisma = new PrismaClient();

const items = [
 {
  name:"T-shirt1",
  description:"great fit,super comfy",
  image:"https://i.png",
  price:25,
  sku:'123'
 },
 {
  name:"T-shirt2",
  description:"great fit,super comfy",
  image:"https://i.png",
  price:25,
  sku:'123'
 },
 {
  name:"T-shirt3",
  description:"great fit,super comfy",
  image:"https://i.png",
  price:25,
  sku:'123'
 }

]

async function main(){
  for(let item of items){
  await prisma.product.create({data:item});
  }
}

 main().catch(err => {
  console.log(err);
  process.exit(1);
 }).finally(() =>{
  prisma.$disconnect()
 })
 
 14)npx prisma db seed --preview-feature
 
 //your database has been seeded
 
 //go to prisma studio ,data is availble  there
 
**generate controller  and service file as a product folder
15)nx generate @nrwl/nest:library products --controller --service

runnig above command give us product directory in lib folder with particular to nest

libs --> products --> src --> lib --> prodcut.* files

libs/products/src/lib/products.controller.ts

import { Controller, Get } from '@nestjs/common';
import { ProductsService } from './products.service';

@Controller('products')
export class ProductsController {
  constructor(private productService: ProductsService) {}

  @Get()
  public async getProducts() {
    return this.productService.getProducts();
  }
}

products.service.ts
import { Injectable } from '@nestjs/common';
import { PrismaClient, Product } from '@prisma/client';

const prisma = new PrismaClient();

@Injectable()
export class ProductsService {
  public getProducts(): Promise<Product[]> {
    return prisma.product.findMany();
  }
}
