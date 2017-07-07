# Mockingoose
![logo]
> A Jest package for mocking mongoose models

## Installation
```bash
$ npm i mockingoose -D
```

## Usage
```js
// user.js
import mongoose from 'mongoose';
const { Schema } = mongoose;

const schema = Schema({
    name: String,
    email: String,
    created: { type: Date, default: Date.now }
})

export default mongoose.model('User', schema);
```

#### mockingoose#ModelName#toReturn(obj, operation = 'find')

```js
// __tests__/user.test.js
import mockingoose from 'mockingoose';
import model from './model';

describe('test mongoose User model', () => {
  it('should return the doc with findById', () => {
    const doc = {
        _id: '507f191e810c19729de860ea',
        name: 'name',
        email: 'name@email.com'
    };
    
    mockingoose.User.toReturn(doc); // operation `find` is default
    
    return model
    .findById({ _id: '507f191e810c19729de860ea'})
    .then(_doc => {
      expect(_doc).toEqual(doc);
    })
  })
  
  it('should return the doc with update', () => {
      const doc = {
          _id: '507f191e810c19729de860ea',
          name: 'name',
          email: 'name@email.com'
      };
      
      mockingoose.User.toReturn(doc, 'update');
      
      return model
      .update({ name: 'changed' }) // this won't really change anything
      .where({ _id: '507f191e810c19729de860ea'})
      .then(_doc => {
        expect(_doc).toEqual(doc);
      })
    })
})
```
### Operation available:

- [x] `find` - for find query
- [x] `findOne` - for findOne query
- [x] `count` - for count query
- [ ] `distinct` - for distinct query
- [ ] `findOneAndUpdate` - for findOneAndUpdate query
- [ ] `findOneAndRemove` - for findOneAndRemove query
- [x] `update` - for update query
- [x] `save` - for create, and save documents `Model.create()` or `Model.save()` or `doc.save()`
- [x] `remove` - for remove query
- [ ] `deleteOne` - for deleteOne query
- [ ] `deleteMany` - for deleteMany query

### Notes
All operations works with `exec` or `promise`.  
the unchecked ones does not work with node callback syntax.

if you are using `Model.create` and you don't pass a mock with mockingoose,  
you'll receive the mongoose created doc (with ObjectId and transformations)

validations are working as expected.

you can simulate Error by passing an Error to mockingoose:

```js
mockingoose.User.toReturn(new Error(), 'save');

return User
    .create({ email: 'name@email.com' })
    .catch(err => {
      expect(err).toBeInstanceOf(Error);
    })
```

no connection is made to the database (mongoose.connect is jest.fn())

[logo]: http://animals.sandiegozoo.org/sites/default/files/2016-12/DwarfMongoose_ZN.jpg
